# backend

Revise this for a server viva. Same shape as the JavaScript notes: a fact, a piece of code, then homework.

The three systems you can defend from work you shipped:

- VMS, vms.arrobin.com. NestJS, Prisma, PostgreSQL, Redis, BullMQ, Socket.IO, JWT, TOTP
- Anemi, anime.arrobin.com. NestJS, Prisma, Socket.IO, FFmpeg, HLS, JWT, TOTP. Anemi does not use Redis, BullMQ, or TanStack Query
- Bracket, bracket.arrobin.com. NestJS, Prisma, Redis, BullMQ, Socket.IO, Stripe

If they ask about GraphQL, gRPC, or Kafka, say you can explain them and you have not operated them.

## The three APIs in one minute

- a module groups a feature
- a controller takes HTTP and calls a service
- a service holds the rule and talks to Prisma
- a guard decides if this caller may enter
- the phone and the website call the same guard. Hiding a button is not a check

Say this: VMS recalculates laytime in a tested function, BullMQ runs it when a fact changes, Socket.IO updates the open screens. Anemi's live part is a Socket.IO room, and the video is FFmpeg. Bracket's bracket, standings, and Elo live in a tested package. Stripe tells us about payment through a webhook.

## REST

- a resource is a noun. `/port-calls/42` is one port call
- GET reads. POST creates. PATCH changes a part. DELETE removes
- the server does not remember the client between requests. The token does
- a body is JSON. A status code says what happened

```ts
@Controller("port-calls")
export class PortCallsController {
  constructor(private readonly portCalls: PortCallsService) {}

  @Get(":id")
  findOne(@Param("id") id: string) {
    return this.portCalls.findOne(id);
  }

  @Post()
  create(@Body() body: unknown) {
    return this.portCalls.create(body);
  }
}
```

Say this: the URL is the thing. The method is the action. The status code is the result.

### homework

- write the four routes for a match: list, one, create, update the score
- say which method each route uses

## HTTP status codes

- 200 OK, the read worked
- 201 Created, the POST made a row
- 204 No Content, the DELETE worked and there is nothing to return
- 400 the client sent a bad shape
- 401 there is no valid login
- 403 the login is valid and this role may not do it
- 404 the id is not there
- 409 the unique rule failed, or the webhook was already stored
- 422 the shape is fine and the business rule rejected it
- 429 too many tries
- 500 we broke. The client cannot fix it by changing the body

Say this: 401 is "who are you". 403 is "I know you, and you may not".

### homework

- a viewer opens `POST /port-calls`. Which status, and why is it not 401?
- a second Stripe event with the same id. Which status do you lean toward, and what do you store?

## NestJS

- Nest is a structure on top of HTTP. Express is the library underneath
- you declare a dependency in the constructor. Nest builds it. You do not call `new PrismaClient()` inside the method
- a pipe validates or transforms the body
- an interceptor wraps the handler for logging or mapping
- an exception filter turns a thrown error into a status code
- you test the service with a fake Prisma

```ts
@Injectable()
export class PortCallsService {
  constructor(private readonly db: PrismaService) {}

  findOne(id: string) {
    return this.db.portCall.findUniqueOrThrow({ where: { id } });
  }
}
```

Say this: Express is the pipe. Nest is the rooms in the building so the next person can find the laytime rule.

### homework

- name controller, service, guard, and pipe, each in one line
- say how you would test the service without a real database

## GraphQL and gRPC

- GraphQL is one endpoint. The client asks for the fields it wants. A resolver reads each field
- the cost is complexity: one query can fan out into many database reads. You limit depth
- gRPC is a typed binary RPC, often between your own services. The contract is a `.proto` file
- I have not shipped either on VMS, Anemi, or Bracket. Our public APIs are HTTP and JSON, with OpenAPI on VMS

Say this: I can draw a resolver and a proto. The systems I run are REST.

### homework

- one sentence on why a client might want GraphQL
- one sentence on why you still shipped REST

## JWT, bcrypt, and TOTP

- the password is never stored. bcrypt hashes it with a salt and a cost
- cost 12 is the figure to say. Higher cost is slower on purpose
- login compares the hash. It does not decrypt the password. A hash is not reversible
- an access token is a JWT, about 15 minutes, and it carries the user id and roles
- the refresh token is long lived, stored as a hash, and rotated. Every refresh deletes the old one and writes a new one
- if a stolen refresh token is used after rotation, revoke the whole family
- TOTP is a six-digit code from an authenticator app. The server stores the secret encrypted
- localStorage is readable by any script on the page. An XSS then becomes a long login. Rotation does not cure XSS. It limits a stolen refresh token to one use

```ts
import { hash, compare } from "bcrypt";

const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
```

Say this: hash the password, short access token, rotate the refresh token, TOTP for the second step.

### homework

- say what you store for the password, the access token, and the refresh token
- say what you do when a refresh token is reused

## OAuth 2.0

- OAuth is a way to let another site log the user in without taking their password
- the authorization server issues a code, then your server trades the code for tokens
- that trade happens on the server. The client secret does not go in the browser
- we sign our own users in with email, password, and TOTP. OAuth is the pattern to explain if they ask about "login with Google"

Say this: I have shipped our own JWT login. I have not put Google login on these three products. The part I would not get wrong is exchanging the code on the server.

### homework

- name the two servers: the one that shows the password page, and the one that receives the code
- say where the client secret lives

## Sessions, cookies, and CSRF

- a session id in a cookie means the browser sends it on every request to that site
- a header token that JavaScript must set is not sent on a cross-site form post
- if the session is a cookie, set `HttpOnly`, `Secure`, and `SameSite`, and check a CSRF token on writes
- CSRF is a site you are logged into being triggered by another site. The defense is the cookie policy plus a token the other site cannot read

Say this: cookie session needs CSRF protection. A bearer token in a header is not sent by a foreign form.

### homework

- list the three cookie flags and one sentence each
- say which of your apps is closer to a bearer token than to a cookie session

## Prisma and a transaction

- the schema is the source of the tables
- a migration is the SQL that changes production
- `prisma generate` updates the client. You do not edit production by hand and hope the schema matches
- a transaction commits several writes together, or none of them
- creating a port call and its first statement-of-facts row is one transaction

```ts
await db.$transaction(async (tx) => {
  const call = await tx.portCall.create({ data: { vesselName, allowedHours } });
  await tx.statementOfFacts.create({ data: { portCallId: call.id } });
  return call;
});
```

```prisma
model PortCall {
  id            String   @id @default(cuid())
  vesselName    String
  allowedHours  Int
  facts         StatementOfFacts[]
}
```

Say this: if the second insert fails, the port call must not remain.

### homework

- name two writes that must commit together on Bracket. Example: the match result and the row that advances the winner
- say what a migration is, in one sentence

## Redis, cache, and rate limit

- Redis is an in-memory data structure server
- on VMS and Bracket it holds rate-limit counters and the BullMQ data
- it is not the database of record. If Redis restarts, a user may get a fresh rate-limit window. Port calls are still in Postgres
- Anemi does not use Redis

```ts
const key = `login:${ip}`;
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
```

- a cache key names the thing and the version of the thing. When the row changes, delete the key or you serve a stale vessel name

Say this: Redis counts and queues. Postgres remembers. Anemi has neither Redis nor a queue.

### homework

- write the key, the increment, and the 429 from memory
- say what is lost if Redis is flushed, and what is not

## Queues and background jobs

- a request that recalculates laytime should not hold the HTTP call open if the work can run after the response
- BullMQ puts a job in Redis. A worker process takes the job
- the job id is stable so the same port call is not calculated twice in parallel: `laytime:${portCallId}`
- if the worker throws, the job retries. The function must be safe to run again
- Anemi does not use BullMQ. Encoding can be a process you start for that upload. Do not describe Anemi as a queue system

```ts
await queue.add(
  "recalculate",
  { portCallId },
  { jobId: `laytime:${portCallId}` },
);
```

Say this: HTTP returns. The worker runs the pure function. The job id stops a double run.

### homework

- give the job id for port call `pc_12`
- say why the laytime function must return the same money if it runs twice on the same facts

## WebSockets and Socket.IO

- HTTP is the client asking. A socket is the server pushing a fact to a room
- Socket.IO is that channel, with a long-polling fallback
- a room is a group of sockets
- Anemi: everyone in a watch session joins `watch:${sessionId}`
- when one person seeks, the server broadcasts position and server time to that room
- authenticate on the handshake from the JWT. Do not trust a user id inside the event body

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

Say this: the room is the session. The token is checked when the socket connects. The event body is not the login.

### homework

- write the room name for session `s_9`
- say which field a late joiner needs besides the position

## Webhooks and idempotency

- a webhook is another server calling yours because something already happened
- Stripe signs the raw body. You verify that signature before you trust the JSON
- Stripe may POST `payment_intent.succeeded` twice
- doing the same operation twice must leave the same result as doing it once. That is idempotency
- store the event id in a unique column. If the insert hits the unique rule, you have already applied it

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);

await db.stripeEvent.create({ data: { id: event.id } });
// unique failure means we already handled this id
```

Say this: verify the signature on the raw body, then ignore an event id you have already stored.

### homework

- say what breaks if you parse JSON before you verify the signature
- say what you store so the second delivery does not record the payment twice

## File uploads

- Anemi accepts a video. The file is not a JSON field
- limit the size. Reject a type you do not transcode
- store the object, then run FFmpeg to HLS. The browser plays a playlist, not the original file in one download
- a playlist is a `.m3u8` file. Segments are short pieces. HLS.js fetches a few seconds at a time
- WebVTT is the caption file

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 6 -hls_list_size 0 -f hls stream.m3u8
```

Say this: the upload is the original. The player reads the playlist. Watch-together is the socket, not the codec.

### homework

- name the playlist extension and the caption format
- say why one giant mp4 is a bad way to watch on a phone network

## Pagination

- do not return every port call in one array
- offset pagination: `?page=2&limit=20`. Simple. A new row inserted at the top shifts the next page
- cursor pagination: `?after=pc_20&limit=20`. Stable when rows are inserted during the scroll
- always return the limit you used, and a cursor or a total if the screen needs it

```ts
const rows = await db.portCall.findMany({
  take: limit,
  skip: 1,
  cursor: cursor ? { id: cursor } : undefined,
  orderBy: { id: "asc" },
});
```

Say this: I page the list. I use a cursor when the list is live and an offset when a person is jumping to page 4.

### homework

- which style fits a vessel table with a page number
- which style fits an infinite scroll of matches

## API versioning

- a breaking change is one that makes an old client wrong: a renamed field, a removed field, a new required field
- adding an optional field is usually safe
- put the version in the path or the header, `/v1/port-calls`, and keep the old version until the phone app has moved
- OpenAPI is the contract VMS generates a client from. Change the contract and regenerate. Do not let the website guess

Say this: the phone app in the store cannot be updated the same afternoon. The old route has to keep working.

### homework

- is adding `notes?: string` a break?
- is renaming `vesselName` to `name` a break?

## Validation

- validate at the edge of the service, in a pipe or with Zod
- the database is the last line: unique, not null, foreign key
- a type in TypeScript is gone at runtime

```ts
const CreatePortCall = z.object({
  vesselName: z.string().min(1),
  allowedHours: z.number().positive(),
});
```

Say this: Zod rejects a bad body with 400. The unique index rejects a duplicate with 409.

### homework

- write the Zod object from memory
- say which error is Zod and which error is Postgres

## Logging and monitoring

- a log line has a request id, the user id, and the error. It does not have the password, the refresh token, or the TOTP secret
- Sentry receives the exception with a release id so you know which deploy broke
- Prometheus scrapes numbers. Grafana draws them
- a useful number is HTTP duration and queue depth. A chart nobody opens is not a monitor

Say this: I can tell which release threw, and I can see the queue getting deep before users tell me.

### homework

- name two fields you will never log
- name two numbers you would put on a Grafana board for VMS

## Tests

- a unit test calls a pure function. Laytime and Elo are unit tests
- an integration test boots the module and hits the database, often in a transaction that rolls back
- an end-to-end test drives the browser. Playwright is that tool
- Vitest runs the Bracket unit tests
- Anemi uses `node:test`, the built-in runner
- say the runner that belongs to the product

```ts
import assert from "node:assert/strict";
import { test } from "node:test";

test("overrun pays demurrage", () => {
  const money = laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 });
  assert.equal(money.demurrage, 7500);
});
```

### homework

- write that test from memory
- say which runner belongs to Anemi and which belongs to Bracket

## Laytime, with numbers

- laytime is the time the contract allows to load or discharge
- the clock starts when the notice of readiness is accepted, subject to the contract's exceptions
- more time than allowed: the charterer pays demurrage to the owner
- finished early: the owner may pay despatch to the charterer, only if the contract says so
- your package is the pure calculation. BullMQ runs it when a fact changes. Socket.IO updates the screens

Worked example. Allowed 72 hours. Notice accepted Monday 08:00. Finished Thursday 20:00. Elapsed 84 hours. Overrun 12 hours. Rate 15,000 dollars a day. 12 / 24 * 15,000 = 7,500 dollars demurrage.

If they finished in 60 hours, time saved is 12 hours. Despatch is often half the demurrage rate, and only when that is the clause. Weather and weekends live in the tested package. Walk those tests. Do not invent a clause in the interview.

```ts
export function laytime(input: { allowedHours: number; usedHours: number; demurragePerDay: number }) {
  const delta = input.usedHours - input.allowedHours;
  const perHour = input.demurragePerDay / 24;
  if (delta > 0) return { demurrage: delta * perHour, despatch: 0 };
  // Half-rate despatch is an example clause. Apply it only when the contract says so.
  return { demurrage: 0, despatch: Math.abs(delta) * perHour * 0.5 };
}
```

### homework

- recompute 72, 84, and 15,000 on paper
- say the sentence about despatch before you mention the 0.5

## Elo

- expected score of A is `1 / (1 + 10 ^ ((Rb - Ra) / 400))`
- A is 1500, B is 1700, K is 32, A wins
- expected score of A is about 0.24
- new rating is about 1500 + 32 * (1 - 0.24) = 1524
- the scheduler and the standings live in the same tested package as this function

```ts
export function expected(ra: number, rb: number): number {
  return 1 / (1 + 10 ** ((rb - ra) / 400));
}

export function nextRating(ra: number, rb: number, score: number, k = 32): number {
  return ra + k * (score - expected(ra, rb));
}
```

Say this: the upset is worth more. A 1500 beating a 1700 moves about 24 points at K = 32.

### homework

- compute the expected score on paper to one decimal
- say what score you pass when A loses

## RBAC

- a role is a bundle of permissions
- the guard reads the role from the verified token and compares it to the route
- the Expo app and the Next.js app must ask the same API
- if the phone hides a button and the API does not check, the phone is only a costume

```ts
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles("operator")
@Post()
create() {
  return this.portCalls.create();
}
```

### homework

- 401 or 403: the token is missing
- 401 or 403: the token is valid and the role is viewer

## Delivery, in the words you need

- Docker packages the process so the server matches the laptop
- Compose starts the API, Postgres, and Redis on one network. Anemi's compose does not need Redis
- nginx is the front door: TLS, the static build, and `/api` proxied to Nest
- Caddy can do the same with automatic certificates. Anemi's notes include Caddy
- PM2 keeps Node alive and restarts it
- GitHub Actions runs tests on a pull request
- inside Docker, `localhost` is the container, not the other service. Use the service name

Say this: I have shipped nginx, PM2, Docker, Compose, and GitHub Actions. I have not operated a Kubernetes cluster. I can still explain a pod as a running container with a restart policy.

### homework

- say why `localhost` from the API container does not reach Postgres
- name the tool that receives exceptions and the tool that draws metrics

## A full spoken answer

Question: walk me through a laytime change.

Answer: the operator edits a fact. The API writes it in a transaction. It enqueues `laytime:` plus the port call id. The worker runs the pure function. 72 allowed and 84 used at 15,000 a day is 7,500 demurrage. Socket.IO tells the open screens. The unit test is why I trust the 7,500. Despatch at half rate only if the contract says so.

## Homework for the whole file

1. Draw HTTP, BullMQ, Postgres, and Socket.IO as four boxes for VMS. Cross out the boxes Anemi does not have.
2. Write the bcrypt line, the rate-limit counter, and the laytime test from memory.
3. Explain 401 and 403 with one viewer and one missing token.
4. Verify a Stripe event in words: raw body, signature, unique event id.
