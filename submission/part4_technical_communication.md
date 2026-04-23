# Part 4: Technical Communication

## Task 4.1: Scenario Response

**Scenario:** The reviewer asks: *"Why did you choose this specific PR over the others? What made it comprehensible to you, and what challenges do you anticipate in implementing it?"*

### Response

Actually, I went with PR #3877 (the "web readonly" feature) because it’s a perfect example of a self-contained security fix that has a big impact without overcomplicating things. Some of the other PRs were definitely cool—like adding whole new plugins—but they usually involved messing around with third-party APIs or complex auth logic that felt a bit out of scope for a clean analysis. This one, however, is all about core web principles: config management, HTTP methods, and request filtering.

My background in Python web development definitely helped here. I’ve worked with Flask a lot, so I have a clear picture of how requests move through an app. I’m really familiar with the "middleware" pattern—using something like `before_request` to catch calls before they hit the main logic. Because of that, I could quickly see how a single config flag could "shield" the whole plugin without having to rewrite every single route.

In terms of challenges, I think the biggest risk is "over-blocking." Sometimes developers use `POST` for complex searches just to avoid URL character limits, even if they aren't actually changing any data. Then there’s the browser stuff—you have to make sure you don't accidentally block `OPTIONS` or `HEAD` requests, which are needed for things like CORS and metadata. If the implementation is too blunt, it could break the UI in ways that aren't immediately obvious.

To handle that, I’d start by auditing all the routes in the `web` plugin to see exactly which methods they use. That way, the read-only logic is only applied where it actually matters. I’d also take a "test-first" approach, specifically looking for those edge cases like pre-flight requests. By focusing on the underlying HTTP protocol first, I can make sure the feature is solid and won't cause headaches for users later.

---

I confirm that this response is my own work and reflects my personal reasoning for choosing this specific task.

