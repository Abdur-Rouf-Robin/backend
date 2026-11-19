# Backend

## REST APIs

- REST is a style for HTTP APIs where a URL names a thing, and the method says what to do with it.
- A resource is a noun: /port-calls/42, not /getPortCall.
- GET reads and must not change data. POST creates. PUT replaces. PATCH changes a part. DELETE removes.
- The server does not remember the client between calls. The token in the header is the memory.
- Use plural nouns, nest only one level when you can, and return the new resource from POST.
- A URL is the name of a box. The method is the verb written on your hand. You do not rename the box for every verb.
- careful: Putting verbs in the path, such as /createPortCall, and then also using POST. Pick the noun and let the method speak.
- VMS and Bracket expose resource URLs. The OpenAPI file is the list of them.
- Why not one POST /api that does everything?
- Because the client, the cache, and the logs can no longer tell a read from a write. GET can be cached and retried. A single doAction endpoint cannot.

```http
GET /port-calls/42
POST /port-calls
PATCH /port-calls/42
DELETE /port-calls/42
```

- homework
```http
GET /port-calls/42
POST /port-calls
PATCH /port-calls/42
DELETE /port-calls/42
```

## HTTP and status codes

- The status code is the one-word result. The body is the explanation.
- 2xx means it worked. 200 is a result, 201 is created, 204 is success with no body.
- 400 is the client's fault in the shape of the request. 401 means not authenticated. 403 means authenticated but not allowed. 404 means not found.
- 409 is a conflict, such as a duplicate name. 422 is a validation failure many APIs use. 429 is rate limited.
- 500 is our bug. 502 and 504 are the proxy or the upstream timing out.
- 401 is the locked door with no key. 403 is the guard who looked at your badge and still said no.
- careful: Returning 200 with { success: false }. The code is part of the contract. Clients and monitors read it.
- Your guards and the rate limiter already depend on 401, 403, and 429.
- What is the difference between 401 and 403?
- 401: I do not know who you are, or the token failed. 403: I know who you are, and this role cannot do this. A viewer who is logged in and opens payroll gets 403, not 401.

```
201 Created + the new port call
401 when the JWT is missing
403 when a viewer hits an operator route
429 when the login counter passes the limit
```

- homework
```
201 Created + the new port call
401 when the JWT is missing
403 when a viewer hits an operator route
429 when the login counter passes the limit
```

## GraphQL

- GraphQL is one endpoint where the client names the fields it wants, instead of a fixed REST response.
- The schema is types and queries. The client sends a query document.
- Resolvers fetch each field. A naive resolver per field can query the database once per row. That is the N+1 problem.
- A DataLoader batches those lookups by id in one round.
- Mutations still change data. Queries should not.
- REST is a set menu. GraphQL is a buffet where the kitchen can still be overwhelmed if every guest asks the chef separately.
- careful: Solving N+1 by hoping. Show the DataLoader or a join.
- You have not shipped GraphQL. Say that, then explain N+1 so they know you understand the cost.
- When would you refuse GraphQL?
- When the screens are known and REST plus OpenAPI already generates the client, which is our case. GraphQL earns its place when many clients need different shapes of a large graph.

```typescript
query {
  portCall(id: "42") { vessel facts { kind at } }
}
```

- homework
```typescript
query {
  portCall(id: "42") { vessel facts { kind at } }
}
```

## gRPC

- gRPC is a binary RPC style. You write a protobuf contract, and the tools generate the client and the server.
- Protobuf names the messages and the methods. The wire format is binary, smaller than JSON.
- It runs on HTTP/2, so many calls can share a connection.
- It fits service-to-service calls inside a network you control.
- Browsers do not speak it comfortably without a proxy, so a public web app usually stays on JSON.
- JSON is a letter anyone can read. Protobuf is a form both offices already printed.
- careful: Using gRPC for a public browser API and then being surprised by proxies and debugging.
- You have not shipped gRPC. Your contract today is OpenAPI.
- REST or gRPC between two of your own services?
- If both are ours and the call is hot, gRPC and a protobuf contract are stricter and faster. If a browser or a third party calls us, I keep JSON.

```typescript
service Laytime {
  rpc Calculate (Facts) returns (Money);
}
```

- homework
```typescript
service Laytime {
  rpc Calculate (Facts) returns (Money);
}
```

## Authentication with JWT

- A JWT is a signed packet. The server can check it without a session table.
- The token is header, payload, and signature. The payload is base64, not encrypted. Do not put secrets in it.
- The server signs with a secret or a private key. Anyone can read the payload. Only the holder of the key can mint a valid signature.
- Keep the access token short. Put the user id and roles in it. Check expiry on every request.
- Store the refresh token hashed, and rotate it.
- The signature is a wax seal. The letter inside is still readable if someone steals it.
- careful: A token that lives for a year, or a token stored where every script can read it, with no rotation.
- This is how VMS, Anemi, and Bracket sign users in.
- Can I hide a password inside a JWT?
- No. The payload is only encoded. Anyone who copies the token can read it. Sign it, do not treat it as a vault.

```json
header.payload.signature
payload: { "sub": "user id", "roles": ["operator"], "exp": 1700000000 }
```

- homework
```json
header.payload.signature
payload: { "sub": "user id", "roles": ["operator"], "exp": 1700000000 }
```

## OAuth 2.0

- OAuth is how a user lets your app act at another service without giving you their password.
- The user is sent to the provider, such as Google. They approve a scope.
- The provider redirects back with a code. The code is not the token.
- Your server exchanges the code, with a client secret, for an access token.
- That token is for the provider's API, not a replacement for your own session, until you decide to issue your own.
- OAuth is a valet key. It starts the car for one garage. It is not the user's house key, and it is not your own JWT until you mint one.
- careful: Putting the client secret in a Next.js page. It belongs in the server environment.
- You use your own JWT for the products. OAuth is the pattern if a later screen says Sign in with Google.
- Why is the code exchange on the server?
- The client secret must not live in the browser. The authorisation code is also one-time, so a stolen redirect is less useful than a stolen long-lived token.

```typescript
browser -> Google consent
Google -> your /callback?code=...
server -> Google: code + client secret
Google -> access token
```

- homework
```typescript
browser -> Google consent
Google -> your /callback?code=...
server -> Google: code + client secret
Google -> access token
```

## Sessions and cookies

- A session is a row on the server. The cookie holds only the random id.
- Login creates a session row and sets a cookie the browser will send back.
- HttpOnly stops JavaScript from reading it. Secure means HTTPS only. SameSite blocks many cross-site posts.
- The server looks up the id. Logging out deletes the row, and the cookie dies everywhere.
- A JWT in a header is not sent automatically. A cookie is. That is why cookies need CSRF care.
- The cookie is a coat-check ticket. The coat stays in our shop. A JWT is a signed note the guest carries.
- careful: A cookie without HttpOnly, or a session id that is the user id.
- Know this so you can explain why CSRF shows up when someone suggests cookie login.
- Cookie session or JWT?
- A session is easy to revoke, because you delete the row. A JWT is easy to scale, because the API does not look up a session. We use short JWTs plus rotating refresh tokens, which is the middle: mostly stateless, with a revoke list for the refresh token.

```http
Set-Cookie: sid=random; HttpOnly; Secure; SameSite=Lax; Path=/
```

- homework
```http
Set-Cookie: sid=random; HttpOnly; Secure; SameSite=Lax; Path=/
```

## Caching with Redis

- A cache stores a computed answer so the next caller skips the slow work.
- Pick a key that includes every input, such as port-call:42.
- Set a TTL so a stale value dies. Or delete the key when the row changes.
- Cache-aside: read cache, on miss read the database, then fill the cache.
- Do not cache a permission decision for long. Do not cache the only copy of the data.
- A cache is a photocopy on the desk. The filing cabinet is still the database. When the file changes, throw the photocopy away.
- careful: Caching a write, or caching without a TTL and then wondering why the screen is old.
- Redis on VMS is the rate-limit counter and the BullMQ store. Say that before you claim a response cache.
- What is a stampede?
- The key expires and a hundred requests all miss and all hit the database. A lock around the rebuild, or a slightly early refresh, lets one request refill the cache.

```typescript
const hit = await redis.get(key);
if (hit) return JSON.parse(hit);
const row = await db.portCall.findUnique({ where: { id } });
await redis.set(key, JSON.stringify(row), "EX", 30);
```

- homework
```typescript
const hit = await redis.get(key);
if (hit) return JSON.parse(hit);
const row = await db.portCall.findUnique({ where: { id } });
await redis.set(key, JSON.stringify(row), "EX", 30);
```

## Message queues

- A queue lets the HTTP request finish while slow work happens somewhere else.
- The producer pushes a message. A consumer pulls it.
- If the consumer crashes, the message returns and another worker can take it.
- At-least-once delivery means you must tolerate duplicates. That is idempotency.
- The queue is not a request-response API. The user is not waiting on that socket.
- A queue is a kitchen printer. The waiter does not cook the order while the guest watches.
- careful: Assuming the job runs once. Design the worker so a second run is harmless.
- BullMQ recalculates laytime and runs Bracket background work.
- Why not just await the email inside the request?
- The user's screen then depends on the mail server. A queue keeps the request short and lets us retry the mail without the user clicking again.

```typescript
await queue.add("email", { userId }, { attempts: 5, removeOnComplete: true });
```

- homework
```typescript
await queue.add("email", { userId }, { attempts: 5, removeOnComplete: true });
```

## Rate limiting

- A rate limit caps how often a caller may hit a route, so one client cannot drown the API or guess passwords.
- Choose the key: IP for login, user id for a signed-in route.
- A fixed window counts hits until the TTL dies. It is simple and a bit unfair at the boundary.
- A sliding window or a token bucket is smoother.
- Return 429 and a Retry-After header. Do not return 500.
- It is a door that only opens ten times a minute. The eleventh person gets a polite closed sign, not a broken door.
- careful: Limiting only in the React button. The attacker will not use your button.
- VMS uses Redis-backed limits on the API.
- Where do you limit first?
- Login, password reset, and any route that sends mail or costs money. Then the public write routes. A high limit on a read of a public page is less urgent.

```typescript
const n = await redis.incr(`login:${ip}`);
if (n === 1) await redis.expire(`login:${ip}`, 60);
if (n > 10) throw new TooManyRequests();
```

- homework
```typescript
const n = await redis.incr(`login:${ip}`);
if (n === 1) await redis.expire(`login:${ip}`, 60);
if (n > 10) throw new TooManyRequests();
```

## WebSockets

- A WebSocket is a connection that stays open so the server can push, instead of the browser asking every second.
- The start is an HTTP upgrade. After that, both sides send frames.
- You must authenticate on connect. A later message that says userId is not proof.
- Rooms keep a message inside one watch session or one tournament.
- Plan for reconnect. Phones drop. The client should resume with the last position.
- Polling is knocking. A socket is leaving the door open and talking.
- careful: Trusting the user id inside the event. Trust the handshake token.
- Anemi watch rooms and Bracket live updates.
- Polling or WebSocket?
- Polling is fine for a screen that can be five seconds old. A shared video playhead cannot. Anemi uses Socket.IO, which prefers WebSocket and can fall back.

```typescript
socket.on("playback", (msg) => apply(msg));
```

- homework
```typescript
socket.on("playback", (msg) => apply(msg));
```

## Background jobs

- A background job is the unit of work on a queue: a name, a payload, and a retry policy.
- Give the job a stable id when the work should not run twice in parallel.
- Set attempts and backoff. A dead database for one second should not lose the laytime run.
- Record failures. A job that fails forever needs a human, which is a dead-letter queue or a Sentry alert.
- Keep the payload small. Pass the port call id, not the whole vessel history.
- The job is a ticket with a number of tries written on it. When the tries run out, the ticket goes to the manager's spike, not the bin.
- careful: Putting the only copy of a file inside the job payload.
- Laytime recalculation is this job.
- What is a dead-letter queue?
- The place a message goes after the retries are spent. I can inspect it, fix the bug, and replay it. I do not silently drop the money calculation.

```typescript
{ jobId: `laytime:${id}`, attempts: 5, backoff: { type: "exponential", delay: 1000 } }
```

- homework
```typescript
{ jobId: `laytime:${id}`, attempts: 5, backoff: { type: "exponential", delay: 1000 } }
```

## Webhooks

- A webhook is their server calling your server when something happens.
- You expose a URL. They POST a fact, such as a Stripe payment.
- Verify a signature. Anyone can POST to a public URL.
- Respond quickly with 2xx, and do the slow work on a queue.
- Store the event id uniquely so a retry does not apply the payment twice.
- A webhook is a phone call from the bank. You check the caller id, you write the call in a book, and you do not credit the account twice because they called again.
- careful: Trusting the JSON without the signature.
- Bracket's Stripe payments.
- Why raw body for the signature?
- The signature is over the exact bytes. If the framework parses JSON and you stringify it again, the bytes change and a real Stripe event looks fake.

```typescript
if (!stripe.webhooks.constructEvent(rawBody, signature, secret)) reject;
await db.stripeEvent.create({ data: { id: event.id } }); // unique
```

- homework
```typescript
if (!stripe.webhooks.constructEvent(rawBody, signature, secret)) reject;
await db.stripeEvent.create({ data: { id: event.id } }); // unique
```

## Idempotency

- An idempotent operation can be repeated and the outcome stays the same as one success.
- GET, PUT of a full document, and DELETE of an id are naturally close to idempotent.
- POST is not, unless the client sends an Idempotency-Key and you store the first response.
- Queue workers and webhooks need it because delivery is at least once.
- A unique constraint is the honest version. A check-then-insert has a race.
- Pressing the lift button twice does not order two lifts. Pressing 'add one bag' twice does. Know which button you built.
- careful: A read of 'does this row exist' followed by an insert, with no unique key, under two parallel requests.
- Stripe events and laytime job ids.
- Is increment idempotent?
- No. Doing it twice adds twice. Setting status to paid is idempotent. Adding one to a counter is not, unless you guard it with a unique event.

```typescript
await db.payment.create({ data: { stripeEventId } });
// second insert throws on the unique stripeEventId, and we return the first result
```

- homework
```typescript
await db.payment.create({ data: { stripeEventId } });
// second insert throws on the unique stripeEventId, and we return the first result
```

## File uploads

- Do not stream a large file through your API process if you can hand the client a direct upload.
- The browser asks the API for a signed upload URL.
- The browser uploads the bytes to object storage. Our Node process does not hold the file.
- The API is told the object key and checks the type and size.
- For Anemi, the uploaded video is then encoded by FFmpeg into HLS. That work is slow, so it is not inside the upload request.
- The API writes the luggage tag. The warehouse receives the suitcase. The factory, FFmpeg, opens it later.
- careful: Trusting the file extension. Check the content type and a size limit, and keep uploads out of the public web root.
- Anemi's upload and HLS encode use object storage. The common cloud name is S3. The idea is the same: store the file, then point the player at the playlist.
- Why not multipart through Nest for a film?
- The Node process becomes a pipe for gigabytes, and a timeout kills the upload. Direct-to-storage keeps the API small. Anemi can still accept an upload and encode it, but the encode is a job, not the request.

```
POST /uploads -> { url, key }
PUT bytes to storage
POST /videos { key } -> enqueue encode
```

- homework
```
POST /uploads -> { url, key }
PUT bytes to storage
POST /videos { key } -> enqueue encode
```

## Pagination

- Pagination returns a page, not the whole table.
- Offset pagination is page and limit. It is easy, and it skips or repeats rows if someone inserts a row while you click.
- Cursor pagination says 'after this id'. It is stable for a live feed.
- Always cap the limit. A client who asks for a million rows is a bug or an attack.
- Return the next cursor, or the total only if the count is cheap.
- Offset is 'skip 40 chairs'. If someone sits down in front, you miss a chair. A cursor is 'the chair after this one'.
- careful: No maximum limit.
- Both web tables and the Expo app need this. Do not return every port call.
- Which one for a vessel list?
- Offset is fine for an admin table with a page number. Cursor is better for an infinite mobile list that is also receiving new rows.

```sql
GET /port-calls?limit=20&after=clx123
SELECT * FROM port_calls WHERE id < $cursor ORDER BY id DESC LIMIT 20;
```

- homework
```sql
GET /port-calls?limit=20&after=clx123
SELECT * FROM port_calls WHERE id < $cursor ORDER BY id DESC LIMIT 20;
```

## API versioning

- Versioning is how you change the contract without breaking the phone app that is already installed.
- A path such as /v2 is obvious. A header is quieter and easier to miss.
- Prefer adding fields over renaming them. Old clients ignore new fields.
- Removing a field or changing a type is a new version.
- The Expo binary in a pocket cannot be updated the same afternoon. The web can.
- It is a new edition of a form. People still have the old paper in a drawer.
- careful: Renaming vessel to shipName in the same day the app store build went out.
- The generated OpenAPI client is the contract the web and the phone share.
- How do you avoid versions?
- Expand the contract. Add the new field, ship the new client, then stop reading the old field, then remove it. Breaking changes are the version you could not avoid.

```typescript
/v1/port-calls still returns arrivedAt
/v2/port-calls adds voyageId and keeps arrivedAt
```

- homework
```typescript
/v1/port-calls still returns arrivedAt
/v2/port-calls adds voyageId and keeps arrivedAt
```

## Input validation

- Validation checks the shape and the business rule before the database sees the value.
- Parse types, lengths, and enums at the edge. Zod or a Nest pipe.
- Reject unknown fields if you are strict, so a client cannot set role: admin on a profile update.
- Check the rule that needs the database in the service, such as 'this vessel is already alongside'.
- Return the field errors, not a stack trace.
- The door policy is the pipe. The safe's own lock is the database constraint. Use both.
- careful: Validating only in the React form.
- Zod and Nest pipes are on your CV.
- Is the database check enough?
- The unique index is the last lock, and you still want it. The friendly error, and the check that does not belong in a constraint, live in the service. Do both.

```typescript
const Body = z.object({ vessel: z.string().min(1).max(80) }).strict();
```

- homework
```typescript
const Body = z.object({ vessel: z.string().min(1).max(80) }).strict();
```

## Logging and monitoring

- A log is a line about one event. A metric is a number over time. A trace is one request across services.
- Log the request id, the user id, the route, and the error. Do not log the password, the token, or the full card number.
- Sentry groups exceptions. Prometheus counts and times them. Grafana draws the counters.
- An alert should mean a human might need to act, such as queue depth stuck high, or error rate up after a deploy.
- Info for normal life, warn for odd, error for a failed job.
- Logs are the diary. Metrics are the thermometer. Alerts are the person who shakes you when the thermometer is high.
- careful: Logging the Authorization header.
- Sentry, Prometheus, and Grafana are on the VMS stack.
- What do you open first when the site is slow?
- The duration graph and the queue depth. Then one slow trace or one slow SQL from the log. I do not start by reading a thousand info lines.

```
requestId=abc userId=12 route=POST /port-calls status=201 ms=40
```

- homework
```
requestId=abc userId=12 route=POST /port-calls status=201 ms=40
```

## Unit and integration tests

- A unit test calls one function with no network. An integration test checks that two real parts work together.
- Laytime and Elo are units. They have no database.
- An integration test can use Postgres and roll the transaction back.
- Mock the edge, such as Stripe, not the function you are trying to trust.
- A test that only asserts that a mock was called does not prove the money is right.
- The unit test is weighing the flour before it goes in the cake. The integration test is tasting the cake.
- careful: Mocking Prisma in every test and never running a query.
- Anemi uses node:test. Bracket uses Vitest. Laytime is the example you write on the board.
- How many tests are enough?
- The pure rules that move money or rankings, the auth guards, and one happy path through the API. A hundred tests of getters are not a safety net.

```typescript
test("early finish can pay despatch", () => {
  const money = laytime({ allowedHours: 72, usedHours: 60, demurragePerDay: 15000 });
  assert.equal(money.despatch, 3750);
});
```

- homework
```typescript
test("early finish can pay despatch", () => {
  const money = laytime({ allowedHours: 72, usedHours: 60, demurragePerDay: 15000 });
  assert.equal(money.despatch, 3750);
});
```


## examples from the projects

- VMS uses NestJS, Prisma, PostgreSQL, Redis, BullMQ, and Socket.IO. A laytime change is a transaction, then a job id `laytime:` plus the port call id, then a socket update.
- allowed 72 hours, used 84, rate 15,000 dollars a day. overrun is 12 hours. demurrage is 7,500 dollars. half-rate despatch only if the contract says so.
- Anemi uses NestJS, Prisma, Socket.IO, and FFmpeg. Anemi does not use Redis, BullMQ, or TanStack Query. the room is `watch:` plus the session id. the playlist is HLS.
- Bracket uses NestJS, Prisma, Redis, BullMQ, Socket.IO, and Stripe. verify the webhook on the raw body. store the event id once. Elo: 1500 beats 1700 at K = 32 and the new rating is about 1524.
- GraphQL, gRPC, and Kafka are in the notes above. the three projects use HTTP and JSON.
