# Backend

## chapter 1 REST APIs

- REST = a style for an HTTP API
- resource = a thing with a name. a port call, a match, a user
- the URL is the name. the method is the action
- collection = the list. `/port-calls`
- item = one row. `/port-calls/42`
- GET reads. GET must not change a row
- POST creates
- PUT replaces the whole item
- PATCH changes only the fields we send
- DELETE removes
- a verb in the path is a smell. `/createPortCall` repeats what POST already says
- the server does not remember the caller between calls. the token is the memory
- nest one level when the child cannot exist alone. `/port-calls/42/facts`
- do not nest three levels. it becomes a sentence nobody can route
- JSON is the body. the header `Content-Type: application/json` says that
- a filter belongs in the query string. `/port-calls?status=open`

```http
GET /port-calls/42
POST /port-calls
PATCH /port-calls/42
DELETE /port-calls/42
```

```ts
@Controller("port-calls")
export class PortCallsController {
  constructor(private readonly portCalls: PortCallsService) {}

  @Get()
  list(@Query("status") status?: string) {
    return this.portCalls.list(status);
  }

  @Get(":id")
  one(@Param("id") id: string) {
    return this.portCalls.one(id);
  }

  @Post()
  create(@Body() body: unknown) {
    return this.portCalls.create(body);
  }

  @Patch(":id")
  update(@Param("id") id: string, @Body() body: unknown) {
    return this.portCalls.update(id, body);
  }
}
```

- line by line. the class is the collection. each method is one action
- `@Get()` with no `:id` is the list. `@Get(":id")` is one row
- the service holds the rule. the controller only receives HTTP
- POST returns the new row, so the client does not guess the id

- homework of chapter 1
```ts
// 1a  write four routes for a match: list, one, create, update the score
// 1b  which method is each route
// 1c  change /createMatch into a noun URL
// 1d  where does ?status=live go, the path or the query string
```

## chapter 2 HTTP and status codes

- status code = the one-word result
- the body is the explanation. the code is the class of result
- 200 OK. a read worked and a body comes back
- 201 Created. POST made a row. send the row
- 204 No Content. DELETE worked and there is nothing to return
- 400 Bad Request. the shape is wrong. a string where a number belongs
- 401 Unauthorized. we do not know who this is. missing or bad token
- 403 Forbidden. we know who this is, and this role may not
- 404 Not Found. the id is not in the table
- 409 Conflict. a unique rule failed, or this webhook id was already stored
- 422 Unprocessable. the shape is fine and the business rule said no
- 429 Too Many Requests. the rate limit fired
- 500 Internal Server Error. our bug. the client cannot fix it by editing the body
- 502 Bad Gateway. nginx reached a process that answered badly
- 504 Gateway Timeout. the upstream did not answer in time

```ts
if (!user) throw new UnauthorizedException();
if (!user.roles.includes("operator")) throw new ForbiddenException();
const row = await db.portCall.findUnique({ where: { id } });
if (!row) throw new NotFoundException();
```

- a viewer who is logged in and calls an operator route gets 403, not 401
- a missing token gets 401
- a second Stripe event with the same id is a 409 or a quiet 200. we must not insert the payment twice
- 500 is for us to read in Sentry. do not send the stack trace to the browser

- homework of chapter 2
```ts
// 2a  logged-out user calls GET /port-calls. which code
// 2b  logged-in viewer calls POST /port-calls. which code
// 2c  PATCH with allowedHours: "noon". which code
// 2d  GET /port-calls/does-not-exist. which code
// 2e  our code throws inside the service. which code, and what must not be in the body
```

## chapter 3 GraphQL and gRPC

- these two are in the picture. the three projects use HTTP and JSON. the notes still have to teach both
- GraphQL = one URL. the client names the fields it wants
- a query reads. a mutation writes
- a resolver is the function for one field
- the cost is fan-out. one query can touch many tables. limit the depth
- gRPC = a typed call between our own services. the contract is a .proto file
- protobuf is the binary shape. it is smaller than JSON and stricter
- we have not shipped GraphQL or gRPC on VMS, Anemi, or Bracket

```graphql
type PortCall {
  id: ID!
  vesselName: String!
  allowedHours: Int!
}

type Query {
  portCall(id: ID!): PortCall
}
```

```protobuf
syntax = "proto3";

service Laytime {
  rpc Quote(QuoteRequest) returns (QuoteReply);
}

message QuoteRequest {
  int32 allowed_hours = 1;
  int32 used_hours = 2;
  int32 demurrage_per_day = 3;
}
```

- a GraphQL field that loads a relation inside a loop is an N+1 query. batch it
- a proto field number is forever. renaming the word is fine. reusing the number is not
- REST is what a browser and a phone can call with fetch. gRPC is what two of our processes would use

- homework of chapter 3
```graphql
# 3a  add a matches field on a Tournament type
# 3b  write a mutation that sets a winner id
```
```protobuf
// 3c  add a score field to QuoteReply as field number 1
// 3d  say why you do not later reuse number 1 for a different meaning
```

## chapter 4 NestJS

- NestJS = a structure for a Node server
- Express is the HTTP library under it. Nest is the rooms in the building
- module = a folder of one feature. it lists controllers and providers
- controller = receives HTTP and calls a service
- service = the rule and the database
- provider = anything Nest can construct. a service is a provider
- guard = may this caller enter
- pipe = validate or transform the body before the method runs
- interceptor = code around the method. logging, mapping
- exception filter = turn a thrown error into a status code
- dependency injection = we name what we need in the constructor. Nest builds it
- we do not write `new PrismaClient()` inside a method. the test can pass a fake

```ts
@Module({
  controllers: [PortCallsController],
  providers: [PortCallsService],
})
export class PortCallsModule {}

@Injectable()
export class PortCallsService {
  constructor(private readonly db: PrismaService) {}

  one(id: string) {
    return this.db.portCall.findUniqueOrThrow({ where: { id } });
  }
}
```

- a module that needs Prisma imports the Prisma module. it does not create a second client
- `findUniqueOrThrow` becomes a 404 when the filter maps it. know which exception that is in our code
- the phone and the website call this same service. a hidden button is not a check

- homework of chapter 4
```ts
// 4a  name controller, service, guard, and pipe in one line each
// 4b  write a MatchesService.one that uses findUniqueOrThrow
// 4c  say how you test it without a real database
```

## chapter 5 Authentication with JWT

- authentication = who is this
- the password is never stored. bcrypt hashes it with a salt and a cost
- cost 12 is the number we use. higher cost is slower on purpose
- login compares the hash. a hash is not decrypted
- access token = a JWT. about 15 minutes. it carries the user id and the roles
- the signature is checked with the server secret. a edited payload fails the check
- refresh token = long lived. we store its hash. every refresh deletes the old one and writes a new one
- rotation = that delete-and-write. if a stolen refresh token is used after rotation, revoke the family
- TOTP = a six-digit code from an authenticator app. the secret is stored encrypted
- localStorage is readable by any script on the page. an XSS then becomes a long login
- rotation does not cure XSS. it limits a stolen refresh token to one use

```ts
import { hash, compare } from "bcrypt";

const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);

// access token claims
{ sub: user.id, roles: user.roles, exp: now + 15 * 60 }
```

- `sub` is the subject, the user id. do not put the password or the TOTP secret in the token
- a JWT is readable by anyone who has it. do not put secrets in the payload. the signature stops edits, not reading
- check `exp` on every request. a token without an expiry is a spare key

- homework of chapter 5
```ts
// 5a  what do we store for the password
// 5b  what do we store for the refresh token
// 5c  what happens when a refresh token is used twice
// 5d  why is the access token short
```

## chapter 6 OAuth, sessions, and cookies

- OAuth 2.0 = another site checks the password, then gives us a code
- our three products sign users in with email, password, and TOTP. OAuth is the pattern for "login with Google"
- the code is traded for tokens on our server. the client secret never goes in the browser
- a session = a row on the server. the browser holds only the session id
- a cookie sends that id on every request to our site
- flags: HttpOnly means JavaScript cannot read it. Secure means HTTPS only. SameSite=Lax blocks many cross-site sends
- CSRF = another site submits a form to us while the browser attaches our cookie
- a CSRF token is a second value the other site cannot read. we check it on writes
- a JWT in the Authorization header is not attached by a foreign form. that is why CSRF shows up when we move the session into a cookie

```http
Set-Cookie: sid=...; HttpOnly; Secure; SameSite=Lax
```

```ts
// the browser must set this header. a foreign form cannot
Authorization: Bearer <access-token>
```

- do the cookie and the CSRF token in the same change. not the cookie first
- CORS is not this chapter. CORS does not stop a form post. SameSite and the CSRF token do

- homework of chapter 6
```ts
// 6a  where does the OAuth client secret live
// 6b  name the three cookie flags
// 6c  why a bearer token in a header is not sent by a foreign form
// 6d  what we add on the same day we switch the session to a cookie
```

## chapter 7 Redis, cache, and rate limit

- Redis = an in-memory data structure server
- on VMS and Bracket it holds rate-limit counters and the BullMQ data
- it is not the database of record. port calls live in Postgres
- if Redis restarts, a user may get a fresh rate-limit window. the rows are still in Postgres
- Anemi does not use Redis
- a cache key names the thing. `port-call:42`
- when the row changes, delete the key, or we serve a stale vessel name
- a counter for login: INCR the key, set a TTL on the first hit, reject after the limit

```ts
const key = `login:${ip}`;
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
```

- INCR is atomic. two logins at once cannot both read 9 and both write 10
- the TTL is the window. 10 tries per 60 seconds, then the key disappears
- do not cache a permission decision for a long time. a role change must be seen
- cache the read of a vessel name. do not cache the laytime money unless the key includes every input

- homework of chapter 7
```ts
// 7a  write the key, the increment, and the 429 from memory
// 7b  what is lost if Redis is flushed
// 7c  what is not lost
// 7d  which product does not use Redis
```

## chapter 8 Queues and background jobs

- a queue = a list of work another process will do
- BullMQ puts the job in Redis. a worker takes it
- the HTTP request returns. the worker runs the function
- the job id is stable so the same port call is not calculated twice at once
- id shape: `laytime:` plus the port call id
- if the worker throws, the job retries. the function must be safe to run again
- idempotent = running it twice leaves the same result as running it once
- Anemi does not use BullMQ. do not describe Anemi as a queue system
- a message queue in general is the same idea. Kafka is the large version. we have not operated Kafka

```ts
await queue.add(
  "recalculate",
  { portCallId },
  { jobId: `laytime:${portCallId}` },
);
```

```ts
export function laytime(input: { allowedHours: number; usedHours: number; demurragePerDay: number }) {
  const delta = input.usedHours - input.allowedHours;
  const perHour = input.demurragePerDay / 24;
  if (delta > 0) return { demurrage: delta * perHour, despatch: 0 };
  // half-rate despatch only when the contract says so
  return { demurrage: 0, despatch: Math.abs(delta) * perHour * 0.5 };
}
// laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 })
// => { demurrage: 7500, despatch: 0 }
```

- allowed 72, used 84, rate 15000 a day. overrun is 12 hours. 12/24 * 15000 = 7500 demurrage
- the worker calls this function. the HTTP handler does not do the arithmetic inline if the job already will
- a failed job stays visible. we log the port call id and the error, not the user's password

- homework of chapter 8
```ts
// 8a  the job id for port call pc_12
// 8b  recompute 72, 84, and 15000 on paper
// 8c  why the function must return the same money if it runs twice on the same facts
// 8d  which product has no queue
```

## chapter 9 WebSockets

- HTTP = the client asks, the server answers, the connection ends
- a socket stays open. the server can push a fact
- WebSocket is the protocol. Socket.IO is the library, with a long-polling fallback
- a room = a group of sockets
- Anemi: everyone in a watch session joins `watch:` plus the session id
- a seek sends position and server time. a late joiner needs both
- authenticate on the handshake from the JWT. do not trust a user id inside the event body
- VMS uses a socket so an open screen updates when laytime is recalculated

```ts
io.use((socket, next) => {
  const user = verifyAccessToken(socket.handshake.auth.token);
  socket.data.userId = user.id;
  next();
});

io.on("connection", (socket) => {
  socket.on("watch:join", ({ sessionId }) => {
    socket.join(`watch:${sessionId}`);
  });

  socket.on("playback:seek", ({ sessionId, position }) => {
    socket.to(`watch:${sessionId}`).emit("playback", {
      position,
      serverTime: Date.now(),
    });
  });
});
```

- `socket.to(room)` sends to the others in the room, not back to the sender
- `io.to(room)` sends to everyone in the room, including the sender
- remove the listener when the screen closes, or an old page still moves the video
- the room name is not a secret. the handshake is the lock

- homework of chapter 9
```ts
// 9a  the room name for session s_9
// 9b  which field a late joiner needs besides position
// 9c  where the JWT is checked
// 9d  socket.to versus io.to
```

## chapter 10 Webhooks, idempotency, and file uploads

- webhook = another server calls us because something already happened
- Stripe signs the raw body. we verify that signature before we trust the JSON
- verify on the raw bytes. if we parse JSON first, the bytes change and the signature fails or, worse, we skip the check
- Stripe may POST payment_intent.succeeded twice
- idempotency = doing it twice leaves the same result as doing it once
- store the event id in a unique column. the second insert hits the unique rule and we stop
- file upload = the bytes are not a JSON field
- limit the size. reject a type we do not handle
- a signed URL lets the browser upload straight to storage. our Node process does not hold the film
- the picture says S3. S3 is the common name for object storage. the idea is the same wherever the file lands
- Anemi then runs FFmpeg. the player reads an HLS playlist, a .m3u8 file, not one giant mp4
- WebVTT is the caption file

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: event.id } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 6 -hls_list_size 0 -f hls stream.m3u8
```

- the upload is the original. the playlist is what the phone plays
- watch-together is the socket from chapter 9, not the codec
- a unique event id is the whole idempotency story for Stripe. do not also "check then insert" without the unique constraint. two deliveries can pass the check together

- homework of chapter 10
```ts
// 10a  what breaks if we JSON.parse before we verify the signature
// 10b  what we store so the second delivery does not record the payment twice
// 10c  the playlist file extension and the caption format
// 10d  why one full mp4 is a bad way to watch on a phone network
```

## chapter 11 Pagination, versioning, and validation

- pagination = do not return every row in one array
- offset = `?page=2&limit=20`. simple. a new row at the top shifts the next page
- cursor = `?after=pc_20&limit=20`. stable while rows are inserted
- a page number fits a vessel table. a cursor fits an infinite scroll
- a breaking change = an old client becomes wrong. a renamed field, a removed field, a new required field
- adding an optional field is usually safe
- put the version in the path. `/v1/port-calls`. keep the old path until the phone app has moved
- the phone in the store cannot be updated the same afternoon
- validation = check the body at the edge, with Zod or a pipe
- TypeScript types are erased. they do not check a request
- the database is the last line. unique, not null, foreign key
- Zod rejects a bad body with 400. a unique index rejects a duplicate with 409

```ts
const CreatePortCall = z.object({
  vesselName: z.string().min(1),
  allowedHours: z.number().positive(),
});

const rows = await db.portCall.findMany({
  take: limit,
  skip: cursor ? 1 : 0,
  cursor: cursor ? { id: cursor } : undefined,
  orderBy: { id: "asc" },
});
```

- `skip: 1` on a cursor query skips the cursor row itself, so the next page starts after it
- OpenAPI is the contract VMS generates a client from. change the contract, then regenerate. do not let the website guess

- homework of chapter 11
```ts
// 11a  offset or cursor for a table with a page number
// 11b  offset or cursor for a live scrolling list
// 11c  is adding notes?: string a break
// 11d  is renaming vesselName to name a break
// 11e  write the Zod object from memory
```

## chapter 12 Logging, tests, and the three APIs

- a log line has a request id, the route, and the error
- it does not have the password, the refresh token, or the TOTP secret
- Sentry receives the exception with a release id, so we know which deploy broke
- Prometheus scrapes numbers. Grafana draws them
- a useful number is HTTP duration and queue depth
- a unit test calls a pure function. laytime and Elo are unit tests
- an integration test boots the module and hits the database, often in a transaction that rolls back
- an end-to-end test drives the browser. Playwright is that tool
- Vitest runs the Bracket unit tests. Anemi uses node:test
- Elo expected score of A is `1 / (1 + 10 ^ ((Rb - Ra) / 400))`
- A is 1500, B is 1700, K is 32, A wins. expected is about 0.24. new rating is about 1524

```ts
import assert from "node:assert/strict";
import { test } from "node:test";

test("overrun pays demurrage", () => {
  const money = laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 });
  assert.equal(money.demurrage, 7500);
});
```

```ts
export function expected(ra: number, rb: number): number {
  return 1 / (1 + 10 ** ((rb - ra) / 400));
}

export function nextRating(ra: number, rb: number, score: number, k = 32): number {
  return ra + k * (score - expected(ra, rb));
}
```

- VMS. NestJS, Prisma, PostgreSQL, Redis, BullMQ, Socket.IO. a fact is a transaction, then job id `laytime:` plus the port call id, then a socket update
- Anemi. NestJS, Prisma, Socket.IO, FFmpeg, HLS. no Redis, no BullMQ, no TanStack Query. room `watch:` plus the session id
- Bracket. NestJS, Prisma, Redis, BullMQ, Socket.IO, Stripe. verify the webhook on the raw body. store the event id once
- Docker packages the process. Compose starts the API, Postgres, and Redis. inside Docker, localhost is the container. use the service name
- nginx is the front door. TLS, the static build, and `/api` proxied to Nest
- Anemi's notes also include Caddy. PM2 keeps Node alive. GitHub Actions runs the tests

- homework of chapter 12
```ts
// 12a  two fields we never log
// 12b  two numbers for a Grafana board on VMS
// 12c  the test above, from memory
// 12d  which runner belongs to Anemi, which to Bracket
// 12e  draw HTTP, BullMQ, Postgres, and Socket.IO for VMS. cross out the boxes Anemi does not have
```
