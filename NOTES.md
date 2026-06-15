# Detective notes

This is your deliverable. We grade the *thinking*, not the word count. Be honest
about what confused you and what unstuck you. Short and real beats long and fake.

---

## Bug 1: the page reloads and my message vanishes

**What I think is wrong (before fixing):**
The HTML `<form>` element has a default browser behavior. When a user submits it, the browser automatically tries to navigate or submit the data by reloading the entire page, which wipes out the current React state.

**What did I ask the AI / what did I look up:**
I checked how to stop the default form submission behavior in modern JavaScript/React applications, focusing on the event handler object.

**What was the solution:**
I added `e.preventDefault();` as the very first line inside the `handleSubmit` function. This explicitly tells the browser to skip its default page-reload routine so that JavaScript can take full control of the submission.

---

## Bug 2: even without the reload, nothing gets saved

**What I think is wrong (before fixing):**
The original `fetch("/api/messages")` call was firing a default `GET` request with an empty body. The Network tab showed a `GET` method instead of a `POST`, and the server route handler had no JSON payload to parse, resulting in nothing being saved.

**What did I ask the AI / what did I look up:**
I looked at the MDN documentation for using the `fetch()` API to send a JSON payload via a `POST` request.

**What was the solution:**
I rebuilt the `fetch` call by explicitly configuring it as a `POST` method. I added the `"Content-Type": "application/json"` header and passed the input states (`name` and `text`) inside `JSON.stringify()` into the `body` field. Finally, I updated the client state locally using `setMessages` right after a successful `201` response to display the message instantly.

---

## Closing reflection

- **Why does a `<form>` reload the page by default, and what does `preventDefault` actually prevent?**
  Forms predate modern client-side JavaScript frameworks. Historically, their native job was to bundle inputs and navigate the browser to a new URL to process the data. `preventDefault` halts this automatic navigation, keeping the user on the current page so React can handle the data smoothly in the background.

- **Why can a GET not carry your message, while a POST can?**
  A `GET` request is designed to retrieve data from a server and does not support a request body; any data it sends must be appended directly to the URL as query parameters. A `POST` request is designed to submit data and contains a dedicated `body` stream meant for carrying payload data securely and without strict length limits.

- **What is the `Content-Type` header telling the server, and what breaks without it?**
  The `Content-Type` header acts as a guide for the server, telling it exactly how to parse the incoming raw bytes (in our case, as JSON). Without it, the server cannot safely assume the data structure, causing functions like Next.js's `request.json()` to fail or throw an error.

---

**Live URL (Vercel):** 