# Part 3: Prompt Preparation

## Task 3.1: Comprehensive Prompt Documentation

I’ve chosen **PR #3877 (Web Read-only Mode)** from the beets repo for this task. It’s a great example to use for prompt engineering because the goal is clear: add a security layer that works across the whole plugin without breaking existing functionality.

---

### 3.1.1 Repository Context
Beets is essentially a powerhouse for music library management. It’s a CLI tool that uses a SQLite database to keep track of music and metadata. Its biggest selling point is the autotagger, which uses MusicBrainz to clean up messy tags and organize files into a clean directory structure automatically.

But beets isn't just a rigid tagger; it's very modular. You can add plugins for almost anything—fetching lyrics, getting album art, or even running a web server to browse your library. The people who use beets are usually serious music collectors who want their library to be perfect and automated. It solves the problem of having thousands of music files with inconsistent metadata by providing a single "source of truth."

### 3.1.2 Pull Request Description
This PR fixes a security hole in the `web` plugin. Before this, the web API was completely wide open. If you had the web server running, anyone who could reach it could delete songs or edit metadata. 

That’s obviously a problem if you just want to share your music with friends or stream it to your phone. This change adds a `readonly` setting that’s turned on by default. When it’s on, the server rejects any "destructive" requests (like POST or DELETE). It basically turns the web UI into a "browse-only" mode. If you actually want to make edits remotely, you have to go into your config and explicitly set `readonly: false`. It’s a classic "secure by default" improvement.

### 3.1.3 What needs to be done (Acceptance Criteria)
*   If `readonly` is true, any POST, DELETE, PUT, or PATCH requests should hit a `405 Method Not Allowed` error.
*   If `readonly` is false, everything should work exactly like it did before.
*   The `readonly` setting needs to be in `beets/config_default.yaml` and set to `true` by default.
*   The check should be centralized (like a Flask `before_request` hook) so we don't have to update every single endpoint.
*   Don't block `GET`, `HEAD`, or `OPTIONS`—we still need those for browsing and browser checks.

### 3.1.4 Things to watch out for (Edge Cases)
*   **Missing config**: If the user doesn't have the `readonly` key in their file, it should just default to `true`.
*   **The UI**: Ideally, we shouldn't even show "Delete" buttons in the web UI if we're in read-only mode, but the backend enforcement is the priority.
*   **Database locks**: Since beets uses SQLite, we need to make sure that allowing writes (when `readonly` is off) doesn't cause weird locking issues, though the core library usually handles that.

### 3.1.5 The Task Prompt
"I need you to implement a 'read-only' mode for the beets `web` plugin. Right now, anyone can edit the library through the web interface, which is a security risk.

First, update `beets/config_default.yaml` to add a `readonly` flag under the `web` section, set to `true`.

Then, in `beetsplug/web/__init__.py`, add a check that runs before every request. If `readonly` is enabled and the request method is POST, PUT, DELETE, or PATCH, return a 405 error. Make sure GET, HEAD, and OPTIONS still work so people can still browse their music.

Finally, update the tests in `tests/test_web.py` to confirm that:
1. Browsing still works in both modes.
2. Deleting a track fails with a 405 when read-only is on.
3. Deleting a track works fine when read-only is turned off.

Keep the code clean and stick to the patterns already used in the web plugin."

---

I confirm that this documentation and the prompt were written by me, based on my own analysis of the beets project and the specific requirements of the PR.

