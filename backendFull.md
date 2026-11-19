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

- example 1. resource = port-calls
- a resource is a noun. this noun is port-calls
- collection = /port-calls
- one item = /port-calls/pc_12
- GET reads the item and must not change it
- POST creates. the body carries vesselName
- PATCH changes only vesselName
- DELETE removes pc_12 and no other row

```http
GET /port-calls
GET /port-calls/pc_12
POST /port-calls
vesselName: Ocean Star
PATCH /port-calls/pc_12
vesselName: Ocean Star
DELETE /port-calls/pc_12
```

```ts
@Controller("port-calls")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { vesselName: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { vesselName?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here pc_12
- POST returns the new row so the client learns the id
- homework
```ts
// 1a
GET /port-calls/pc_12
// 1b
POST /port-calls  body vesselName = Ocean Star
// 1c  rewrite /createport-calls as a noun URL
// 1d  /port-calls?status=open is a filter, not a new resource
```

- example 2. resource = matches
- a resource is a noun. this noun is matches
- collection = /matches
- one item = /matches/m_1
- GET reads the item and must not change it
- POST creates. the body carries home
- PATCH changes only home
- DELETE removes m_1 and no other row

```http
GET /matches
GET /matches/m_1
POST /matches
home: Ada
PATCH /matches/m_1
home: Ada
DELETE /matches/m_1
```

```ts
@Controller("matches")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { home: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { home?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here m_1
- POST returns the new row so the client learns the id
- homework
```ts
// 2a
GET /matches/m_1
// 2b
POST /matches  body home = Ada
// 2c  rewrite /creatematches as a noun URL
// 2d  /matches?status=open is a filter, not a new resource
```

- example 3. resource = tournaments
- a resource is a noun. this noun is tournaments
- collection = /tournaments
- one item = /tournaments/t_9
- GET reads the item and must not change it
- POST creates. the body carries name
- PATCH changes only name
- DELETE removes t_9 and no other row

```http
GET /tournaments
GET /tournaments/t_9
POST /tournaments
name: Rajshahi Open
PATCH /tournaments/t_9
name: Rajshahi Open
DELETE /tournaments/t_9
```

```ts
@Controller("tournaments")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { name: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { name?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here t_9
- POST returns the new row so the client learns the id
- homework
```ts
// 3a
GET /tournaments/t_9
// 3b
POST /tournaments  body name = Rajshahi Open
// 3c  rewrite /createtournaments as a noun URL
// 3d  /tournaments?status=open is a filter, not a new resource
```

- example 4. resource = videos
- a resource is a noun. this noun is videos
- collection = /videos
- one item = /videos/v_4
- GET reads the item and must not change it
- POST creates. the body carries title
- PATCH changes only title
- DELETE removes v_4 and no other row

```http
GET /videos
GET /videos/v_4
POST /videos
title: Episode 1
PATCH /videos/v_4
title: Episode 1
DELETE /videos/v_4
```

```ts
@Controller("videos")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { title: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { title?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here v_4
- POST returns the new row so the client learns the id
- homework
```ts
// 4a
GET /videos/v_4
// 4b
POST /videos  body title = Episode 1
// 4c  rewrite /createvideos as a noun URL
// 4d  /videos?status=open is a filter, not a new resource
```

- example 5. resource = facts
- a resource is a noun. this noun is facts
- collection = /facts
- one item = /facts/f_2
- GET reads the item and must not change it
- POST creates. the body carries kind
- PATCH changes only kind
- DELETE removes f_2 and no other row

```http
GET /facts
GET /facts/f_2
POST /facts
kind: NOR
PATCH /facts/f_2
kind: NOR
DELETE /facts/f_2
```

```ts
@Controller("facts")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { kind: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { kind?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here f_2
- POST returns the new row so the client learns the id
- homework
```ts
// 5a
GET /facts/f_2
// 5b
POST /facts  body kind = NOR
// 5c  rewrite /createfacts as a noun URL
// 5d  /facts?status=open is a filter, not a new resource
```

- example 6. resource = users
- a resource is a noun. this noun is users
- collection = /users
- one item = /users/u_3
- GET reads the item and must not change it
- POST creates. the body carries email
- PATCH changes only email
- DELETE removes u_3 and no other row

```http
GET /users
GET /users/u_3
POST /users
email: a@example.com
PATCH /users/u_3
email: a@example.com
DELETE /users/u_3
```

```ts
@Controller("users")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { email: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { email?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here u_3
- POST returns the new row so the client learns the id
- homework
```ts
// 6a
GET /users/u_3
// 6b
POST /users  body email = a@example.com
// 6c  rewrite /createusers as a noun URL
// 6d  /users?status=open is a filter, not a new resource
```

- example 7. resource = payments
- a resource is a noun. this noun is payments
- collection = /payments
- one item = /payments/pay_8
- GET reads the item and must not change it
- POST creates. the body carries eventId
- PATCH changes only eventId
- DELETE removes pay_8 and no other row

```http
GET /payments
GET /payments/pay_8
POST /payments
eventId: evt_1
PATCH /payments/pay_8
eventId: evt_1
DELETE /payments/pay_8
```

```ts
@Controller("payments")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { eventId: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { eventId?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here pay_8
- POST returns the new row so the client learns the id
- homework
```ts
// 7a
GET /payments/pay_8
// 7b
POST /payments  body eventId = evt_1
// 7c  rewrite /createpayments as a noun URL
// 7d  /payments?status=open is a filter, not a new resource
```

- example 8. resource = rooms
- a resource is a noun. this noun is rooms
- collection = /rooms
- one item = /rooms/room_2
- GET reads the item and must not change it
- POST creates. the body carries sessionId
- PATCH changes only sessionId
- DELETE removes room_2 and no other row

```http
GET /rooms
GET /rooms/room_2
POST /rooms
sessionId: s_9
PATCH /rooms/room_2
sessionId: s_9
DELETE /rooms/room_2
```

```ts
@Controller("rooms")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { sessionId: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { sessionId?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here room_2
- POST returns the new row so the client learns the id
- homework
```ts
// 8a
GET /rooms/room_2
// 8b
POST /rooms  body sessionId = s_9
// 8c  rewrite /createrooms as a noun URL
// 8d  /rooms?status=open is a filter, not a new resource
```

- example 9. resource = vessels
- a resource is a noun. this noun is vessels
- collection = /vessels
- one item = /vessels/vs_7
- GET reads the item and must not change it
- POST creates. the body carries name
- PATCH changes only name
- DELETE removes vs_7 and no other row

```http
GET /vessels
GET /vessels/vs_7
POST /vessels
name: North Wind
PATCH /vessels/vs_7
name: North Wind
DELETE /vessels/vs_7
```

```ts
@Controller("vessels")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { name: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { name?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here vs_7
- POST returns the new row so the client learns the id
- homework
```ts
// 9a
GET /vessels/vs_7
// 9b
POST /vessels  body name = North Wind
// 9c  rewrite /createvessels as a noun URL
// 9d  /vessels?status=open is a filter, not a new resource
```

- example 10. resource = scores
- a resource is a noun. this noun is scores
- collection = /scores
- one item = /scores/sc_3
- GET reads the item and must not change it
- POST creates. the body carries points
- PATCH changes only points
- DELETE removes sc_3 and no other row

```http
GET /scores
GET /scores/sc_3
POST /scores
points: 11
PATCH /scores/sc_3
points: 11
DELETE /scores/sc_3
```

```ts
@Controller("scores")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { points: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { points?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here sc_3
- POST returns the new row so the client learns the id
- homework
```ts
// 10a
GET /scores/sc_3
// 10b
POST /scores  body points = 11
// 10c  rewrite /createscores as a noun URL
// 10d  /scores?status=open is a filter, not a new resource
```

- example 11. resource = teams
- a resource is a noun. this noun is teams
- collection = /teams
- one item = /teams/tm_2
- GET reads the item and must not change it
- POST creates. the body carries name
- PATCH changes only name
- DELETE removes tm_2 and no other row

```http
GET /teams
GET /teams/tm_2
POST /teams
name: Blue
PATCH /teams/tm_2
name: Blue
DELETE /teams/tm_2
```

```ts
@Controller("teams")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { name: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { name?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here tm_2
- POST returns the new row so the client learns the id
- homework
```ts
// 11a
GET /teams/tm_2
// 11b
POST /teams  body name = Blue
// 11c  rewrite /createteams as a noun URL
// 11d  /teams?status=open is a filter, not a new resource
```

- example 12. resource = uploads
- a resource is a noun. this noun is uploads
- collection = /uploads
- one item = /uploads/up_6
- GET reads the item and must not change it
- POST creates. the body carries key
- PATCH changes only key
- DELETE removes up_6 and no other row

```http
GET /uploads
GET /uploads/up_6
POST /uploads
key: videos/u_1/a.mp4
PATCH /uploads/up_6
key: videos/u_1/a.mp4
DELETE /uploads/up_6
```

```ts
@Controller("uploads")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { key: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { key?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here up_6
- POST returns the new row so the client learns the id
- homework
```ts
// 12a
GET /uploads/up_6
// 12b
POST /uploads  body key = videos/u_1/a.mp4
// 12c  rewrite /createuploads as a noun URL
// 12d  /uploads?status=open is a filter, not a new resource
```

- example 13. resource = standings
- a resource is a noun. this noun is standings
- collection = /standings
- one item = /standings/st_1
- GET reads the item and must not change it
- POST creates. the body carries player
- PATCH changes only player
- DELETE removes st_1 and no other row

```http
GET /standings
GET /standings/st_1
POST /standings
player: player_a
PATCH /standings/st_1
player: player_a
DELETE /standings/st_1
```

```ts
@Controller("standings")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { player: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { player?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here st_1
- POST returns the new row so the client learns the id
- homework
```ts
// 13a
GET /standings/st_1
// 13b
POST /standings  body player = player_a
// 13c  rewrite /createstandings as a noun URL
// 13d  /standings?status=open is a filter, not a new resource
```

- example 14. resource = notices
- a resource is a noun. this noun is notices
- collection = /notices
- one item = /notices/n_2
- GET reads the item and must not change it
- POST creates. the body carries port
- PATCH changes only port
- DELETE removes n_2 and no other row

```http
GET /notices
GET /notices/n_2
POST /notices
port: Chattogram
PATCH /notices/n_2
port: Chattogram
DELETE /notices/n_2
```

```ts
@Controller("notices")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { port: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { port?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here n_2
- POST returns the new row so the client learns the id
- homework
```ts
// 14a
GET /notices/n_2
// 14b
POST /notices  body port = Chattogram
// 14c  rewrite /createnotices as a noun URL
// 14d  /notices?status=open is a filter, not a new resource
```

- example 15. resource = brackets
- a resource is a noun. this noun is brackets
- collection = /brackets
- one item = /brackets/b_3
- GET reads the item and must not change it
- POST creates. the body carries round
- PATCH changes only round
- DELETE removes b_3 and no other row

```http
GET /brackets
GET /brackets/b_3
POST /brackets
round: semi
PATCH /brackets/b_3
round: semi
DELETE /brackets/b_3
```

```ts
@Controller("brackets")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { round: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { round?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here b_3
- POST returns the new row so the client learns the id
- homework
```ts
// 15a
GET /brackets/b_3
// 15b
POST /brackets  body round = semi
// 15c  rewrite /createbrackets as a noun URL
// 15d  /brackets?status=open is a filter, not a new resource
```

- example 16. resource = sessions
- a resource is a noun. this noun is sessions
- collection = /sessions
- one item = /sessions/se_1
- GET reads the item and must not change it
- POST creates. the body carries userId
- PATCH changes only userId
- DELETE removes se_1 and no other row

```http
GET /sessions
GET /sessions/se_1
POST /sessions
userId: u_3
PATCH /sessions/se_1
userId: u_3
DELETE /sessions/se_1
```

```ts
@Controller("sessions")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { userId: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { userId?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here se_1
- POST returns the new row so the client learns the id
- homework
```ts
// 16a
GET /sessions/se_1
// 16b
POST /sessions  body userId = u_3
// 16c  rewrite /createsessions as a noun URL
// 16d  /sessions?status=open is a filter, not a new resource
```

- example 17. resource = queues
- a resource is a noun. this noun is queues
- collection = /queues
- one item = /queues/q_1
- GET reads the item and must not change it
- POST creates. the body carries name
- PATCH changes only name
- DELETE removes q_1 and no other row

```http
GET /queues
GET /queues/q_1
POST /queues
name: laytime
PATCH /queues/q_1
name: laytime
DELETE /queues/q_1
```

```ts
@Controller("queues")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { name: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { name?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here q_1
- POST returns the new row so the client learns the id
- homework
```ts
// 17a
GET /queues/q_1
// 17b
POST /queues  body name = laytime
// 17c  rewrite /createqueues as a noun URL
// 17d  /queues?status=open is a filter, not a new resource
```

- example 18. resource = roles
- a resource is a noun. this noun is roles
- collection = /roles
- one item = /roles/r_1
- GET reads the item and must not change it
- POST creates. the body carries name
- PATCH changes only name
- DELETE removes r_1 and no other row

```http
GET /roles
GET /roles/r_1
POST /roles
name: operator
PATCH /roles/r_1
name: operator
DELETE /roles/r_1
```

```ts
@Controller("roles")
export class Handler {
  @Get()
  list() {
    return this.service.list();
  }
  @Get(":id")
  one(@Param("id") id: string) {
    return this.service.one(id);
  }
  @Post()
  create(@Body() body: { name: string }) {
    return this.service.create(body);
  }
  @Patch(":id")
  update(@Param("id") id: string, @Body() body: { name?: string }) {
    return this.service.update(id, body);
  }
}
```

- the class name is the collection
- @Get() with no id is the list
- @Get(":id") is one row, here r_1
- POST returns the new row so the client learns the id
- homework
```ts
// 18a
GET /roles/r_1
// 18b
POST /roles  body name = operator
// 18c  rewrite /createroles as a noun URL
// 18d  /roles?status=open is a filter, not a new resource
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

- example 1. status codes for port-calls pc_12
- 200 = GET /port-calls/pc_12 found the row
- 201 = POST /port-calls created a row whose vesselName is Ocean Star
- 204 = DELETE /port-calls/pc_12 removed it and the body is empty
- 400 = vesselName was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /port-calls
- 403 = a viewer called POST /port-calls
- 404 = GET /port-calls/missing
- 409 = a second insert used the same id pc_12
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /port-calls
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.port_calls.findUnique({ where: { id: "pc_12" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 1a  logged out GET /port-calls -> 401
// 1b  viewer POST /port-calls -> 403
// 1c  GET /port-calls/no-such -> 404
// 1d  POST a valid port-calls -> 201 and the body contains vesselName
// 1e  our throw inside the service -> 500, no stack in the JSON
```

- example 2. status codes for matches m_1
- 200 = GET /matches/m_1 found the row
- 201 = POST /matches created a row whose home is Ada
- 204 = DELETE /matches/m_1 removed it and the body is empty
- 400 = home was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /matches
- 403 = a viewer called POST /matches
- 404 = GET /matches/missing
- 409 = a second insert used the same id m_1
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /matches
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.matches.findUnique({ where: { id: "m_1" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 2a  logged out GET /matches -> 401
// 2b  viewer POST /matches -> 403
// 2c  GET /matches/no-such -> 404
// 2d  POST a valid matches -> 201 and the body contains home
// 2e  our throw inside the service -> 500, no stack in the JSON
```

- example 3. status codes for tournaments t_9
- 200 = GET /tournaments/t_9 found the row
- 201 = POST /tournaments created a row whose name is Rajshahi Open
- 204 = DELETE /tournaments/t_9 removed it and the body is empty
- 400 = name was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /tournaments
- 403 = a viewer called POST /tournaments
- 404 = GET /tournaments/missing
- 409 = a second insert used the same id t_9
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /tournaments
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.tournaments.findUnique({ where: { id: "t_9" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 3a  logged out GET /tournaments -> 401
// 3b  viewer POST /tournaments -> 403
// 3c  GET /tournaments/no-such -> 404
// 3d  POST a valid tournaments -> 201 and the body contains name
// 3e  our throw inside the service -> 500, no stack in the JSON
```

- example 4. status codes for videos v_4
- 200 = GET /videos/v_4 found the row
- 201 = POST /videos created a row whose title is Episode 1
- 204 = DELETE /videos/v_4 removed it and the body is empty
- 400 = title was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /videos
- 403 = a viewer called POST /videos
- 404 = GET /videos/missing
- 409 = a second insert used the same id v_4
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /videos
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.videos.findUnique({ where: { id: "v_4" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 4a  logged out GET /videos -> 401
// 4b  viewer POST /videos -> 403
// 4c  GET /videos/no-such -> 404
// 4d  POST a valid videos -> 201 and the body contains title
// 4e  our throw inside the service -> 500, no stack in the JSON
```

- example 5. status codes for facts f_2
- 200 = GET /facts/f_2 found the row
- 201 = POST /facts created a row whose kind is NOR
- 204 = DELETE /facts/f_2 removed it and the body is empty
- 400 = kind was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /facts
- 403 = a viewer called POST /facts
- 404 = GET /facts/missing
- 409 = a second insert used the same id f_2
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /facts
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.facts.findUnique({ where: { id: "f_2" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 5a  logged out GET /facts -> 401
// 5b  viewer POST /facts -> 403
// 5c  GET /facts/no-such -> 404
// 5d  POST a valid facts -> 201 and the body contains kind
// 5e  our throw inside the service -> 500, no stack in the JSON
```

- example 6. status codes for users u_3
- 200 = GET /users/u_3 found the row
- 201 = POST /users created a row whose email is a@example.com
- 204 = DELETE /users/u_3 removed it and the body is empty
- 400 = email was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /users
- 403 = a viewer called POST /users
- 404 = GET /users/missing
- 409 = a second insert used the same id u_3
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /users
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.users.findUnique({ where: { id: "u_3" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 6a  logged out GET /users -> 401
// 6b  viewer POST /users -> 403
// 6c  GET /users/no-such -> 404
// 6d  POST a valid users -> 201 and the body contains email
// 6e  our throw inside the service -> 500, no stack in the JSON
```

- example 7. status codes for payments pay_8
- 200 = GET /payments/pay_8 found the row
- 201 = POST /payments created a row whose eventId is evt_1
- 204 = DELETE /payments/pay_8 removed it and the body is empty
- 400 = eventId was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /payments
- 403 = a viewer called POST /payments
- 404 = GET /payments/missing
- 409 = a second insert used the same id pay_8
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /payments
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.payments.findUnique({ where: { id: "pay_8" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 7a  logged out GET /payments -> 401
// 7b  viewer POST /payments -> 403
// 7c  GET /payments/no-such -> 404
// 7d  POST a valid payments -> 201 and the body contains eventId
// 7e  our throw inside the service -> 500, no stack in the JSON
```

- example 8. status codes for rooms room_2
- 200 = GET /rooms/room_2 found the row
- 201 = POST /rooms created a row whose sessionId is s_9
- 204 = DELETE /rooms/room_2 removed it and the body is empty
- 400 = sessionId was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /rooms
- 403 = a viewer called POST /rooms
- 404 = GET /rooms/missing
- 409 = a second insert used the same id room_2
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /rooms
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.rooms.findUnique({ where: { id: "room_2" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 8a  logged out GET /rooms -> 401
// 8b  viewer POST /rooms -> 403
// 8c  GET /rooms/no-such -> 404
// 8d  POST a valid rooms -> 201 and the body contains sessionId
// 8e  our throw inside the service -> 500, no stack in the JSON
```

- example 9. status codes for vessels vs_7
- 200 = GET /vessels/vs_7 found the row
- 201 = POST /vessels created a row whose name is North Wind
- 204 = DELETE /vessels/vs_7 removed it and the body is empty
- 400 = name was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /vessels
- 403 = a viewer called POST /vessels
- 404 = GET /vessels/missing
- 409 = a second insert used the same id vs_7
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /vessels
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.vessels.findUnique({ where: { id: "vs_7" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 9a  logged out GET /vessels -> 401
// 9b  viewer POST /vessels -> 403
// 9c  GET /vessels/no-such -> 404
// 9d  POST a valid vessels -> 201 and the body contains name
// 9e  our throw inside the service -> 500, no stack in the JSON
```

- example 10. status codes for scores sc_3
- 200 = GET /scores/sc_3 found the row
- 201 = POST /scores created a row whose points is 11
- 204 = DELETE /scores/sc_3 removed it and the body is empty
- 400 = points was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /scores
- 403 = a viewer called POST /scores
- 404 = GET /scores/missing
- 409 = a second insert used the same id sc_3
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /scores
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.scores.findUnique({ where: { id: "sc_3" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 10a  logged out GET /scores -> 401
// 10b  viewer POST /scores -> 403
// 10c  GET /scores/no-such -> 404
// 10d  POST a valid scores -> 201 and the body contains points
// 10e  our throw inside the service -> 500, no stack in the JSON
```

- example 11. status codes for teams tm_2
- 200 = GET /teams/tm_2 found the row
- 201 = POST /teams created a row whose name is Blue
- 204 = DELETE /teams/tm_2 removed it and the body is empty
- 400 = name was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /teams
- 403 = a viewer called POST /teams
- 404 = GET /teams/missing
- 409 = a second insert used the same id tm_2
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /teams
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.teams.findUnique({ where: { id: "tm_2" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 11a  logged out GET /teams -> 401
// 11b  viewer POST /teams -> 403
// 11c  GET /teams/no-such -> 404
// 11d  POST a valid teams -> 201 and the body contains name
// 11e  our throw inside the service -> 500, no stack in the JSON
```

- example 12. status codes for uploads up_6
- 200 = GET /uploads/up_6 found the row
- 201 = POST /uploads created a row whose key is videos/u_1/a.mp4
- 204 = DELETE /uploads/up_6 removed it and the body is empty
- 400 = key was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /uploads
- 403 = a viewer called POST /uploads
- 404 = GET /uploads/missing
- 409 = a second insert used the same id up_6
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /uploads
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.uploads.findUnique({ where: { id: "up_6" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 12a  logged out GET /uploads -> 401
// 12b  viewer POST /uploads -> 403
// 12c  GET /uploads/no-such -> 404
// 12d  POST a valid uploads -> 201 and the body contains key
// 12e  our throw inside the service -> 500, no stack in the JSON
```

- example 13. status codes for standings st_1
- 200 = GET /standings/st_1 found the row
- 201 = POST /standings created a row whose player is player_a
- 204 = DELETE /standings/st_1 removed it and the body is empty
- 400 = player was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /standings
- 403 = a viewer called POST /standings
- 404 = GET /standings/missing
- 409 = a second insert used the same id st_1
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /standings
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.standings.findUnique({ where: { id: "st_1" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 13a  logged out GET /standings -> 401
// 13b  viewer POST /standings -> 403
// 13c  GET /standings/no-such -> 404
// 13d  POST a valid standings -> 201 and the body contains player
// 13e  our throw inside the service -> 500, no stack in the JSON
```

- example 14. status codes for notices n_2
- 200 = GET /notices/n_2 found the row
- 201 = POST /notices created a row whose port is Chattogram
- 204 = DELETE /notices/n_2 removed it and the body is empty
- 400 = port was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /notices
- 403 = a viewer called POST /notices
- 404 = GET /notices/missing
- 409 = a second insert used the same id n_2
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /notices
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.notices.findUnique({ where: { id: "n_2" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 14a  logged out GET /notices -> 401
// 14b  viewer POST /notices -> 403
// 14c  GET /notices/no-such -> 404
// 14d  POST a valid notices -> 201 and the body contains port
// 14e  our throw inside the service -> 500, no stack in the JSON
```

- example 15. status codes for brackets b_3
- 200 = GET /brackets/b_3 found the row
- 201 = POST /brackets created a row whose round is semi
- 204 = DELETE /brackets/b_3 removed it and the body is empty
- 400 = round was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /brackets
- 403 = a viewer called POST /brackets
- 404 = GET /brackets/missing
- 409 = a second insert used the same id b_3
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /brackets
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.brackets.findUnique({ where: { id: "b_3" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 15a  logged out GET /brackets -> 401
// 15b  viewer POST /brackets -> 403
// 15c  GET /brackets/no-such -> 404
// 15d  POST a valid brackets -> 201 and the body contains round
// 15e  our throw inside the service -> 500, no stack in the JSON
```

- example 16. status codes for sessions se_1
- 200 = GET /sessions/se_1 found the row
- 201 = POST /sessions created a row whose userId is u_3
- 204 = DELETE /sessions/se_1 removed it and the body is empty
- 400 = userId was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /sessions
- 403 = a viewer called POST /sessions
- 404 = GET /sessions/missing
- 409 = a second insert used the same id se_1
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /sessions
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.sessions.findUnique({ where: { id: "se_1" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 16a  logged out GET /sessions -> 401
// 16b  viewer POST /sessions -> 403
// 16c  GET /sessions/no-such -> 404
// 16d  POST a valid sessions -> 201 and the body contains userId
// 16e  our throw inside the service -> 500, no stack in the JSON
```

- example 17. status codes for queues q_1
- 200 = GET /queues/q_1 found the row
- 201 = POST /queues created a row whose name is laytime
- 204 = DELETE /queues/q_1 removed it and the body is empty
- 400 = name was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /queues
- 403 = a viewer called POST /queues
- 404 = GET /queues/missing
- 409 = a second insert used the same id q_1
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /queues
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.queues.findUnique({ where: { id: "q_1" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 17a  logged out GET /queues -> 401
// 17b  viewer POST /queues -> 403
// 17c  GET /queues/no-such -> 404
// 17d  POST a valid queues -> 201 and the body contains name
// 17e  our throw inside the service -> 500, no stack in the JSON
```

- example 18. status codes for roles r_1
- 200 = GET /roles/r_1 found the row
- 201 = POST /roles created a row whose name is operator
- 204 = DELETE /roles/r_1 removed it and the body is empty
- 400 = name was sent as a number of the wrong shape, or was empty
- 401 = no token on GET /roles
- 403 = a viewer called POST /roles
- 404 = GET /roles/missing
- 409 = a second insert used the same id r_1
- 429 = the login or the write limit fired
- 500 = our bug. the stack trace stays in the log

```ts
if (!user) throw new UnauthorizedException(); // 401 on /roles
if (!user.roles.includes("operator")) throw new ForbiddenException(); // 403
const row = await db.roles.findUnique({ where: { id: "r_1" } });
if (!row) throw new NotFoundException(); // 404
return row; // 200
```

- homework
```ts
// 18a  logged out GET /roles -> 401
// 18b  viewer POST /roles -> 403
// 18c  GET /roles/no-such -> 404
// 18d  POST a valid roles -> 201 and the body contains name
// 18e  our throw inside the service -> 500, no stack in the JSON
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

- example 1. GraphQL and gRPC for port-calls
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type port-calls has id and vesselName
- a query reads pc_12. a mutation writes vesselName
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  vesselName: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, vesselName: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string vesselName = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 1a  query item id pc_12 and ask only for vesselName
# 1b  mutation setField id pc_12 value Ocean Star
# 1c  do not reuse protobuf field number 1 for a new meaning later
```

- example 2. GraphQL and gRPC for matches
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type matches has id and home
- a query reads m_1. a mutation writes home
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  home: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, home: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string home = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 2a  query item id m_1 and ask only for home
# 2b  mutation setField id m_1 value Ada
# 2c  do not reuse protobuf field number 1 for a new meaning later
```

- example 3. GraphQL and gRPC for tournaments
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type tournaments has id and name
- a query reads t_9. a mutation writes name
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  name: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, name: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string name = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 3a  query item id t_9 and ask only for name
# 3b  mutation setField id t_9 value Rajshahi Open
# 3c  do not reuse protobuf field number 1 for a new meaning later
```

- example 4. GraphQL and gRPC for videos
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type videos has id and title
- a query reads v_4. a mutation writes title
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  title: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, title: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string title = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 4a  query item id v_4 and ask only for title
# 4b  mutation setField id v_4 value Episode 1
# 4c  do not reuse protobuf field number 1 for a new meaning later
```

- example 5. GraphQL and gRPC for facts
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type facts has id and kind
- a query reads f_2. a mutation writes kind
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  kind: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, kind: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string kind = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 5a  query item id f_2 and ask only for kind
# 5b  mutation setField id f_2 value NOR
# 5c  do not reuse protobuf field number 1 for a new meaning later
```

- example 6. GraphQL and gRPC for users
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type users has id and email
- a query reads u_3. a mutation writes email
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  email: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, email: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string email = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 6a  query item id u_3 and ask only for email
# 6b  mutation setField id u_3 value a@example.com
# 6c  do not reuse protobuf field number 1 for a new meaning later
```

- example 7. GraphQL and gRPC for payments
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type payments has id and eventId
- a query reads pay_8. a mutation writes eventId
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  eventId: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, eventId: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string eventId = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 7a  query item id pay_8 and ask only for eventId
# 7b  mutation setField id pay_8 value evt_1
# 7c  do not reuse protobuf field number 1 for a new meaning later
```

- example 8. GraphQL and gRPC for rooms
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type rooms has id and sessionId
- a query reads room_2. a mutation writes sessionId
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  sessionId: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, sessionId: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string sessionId = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 8a  query item id room_2 and ask only for sessionId
# 8b  mutation setField id room_2 value s_9
# 8c  do not reuse protobuf field number 1 for a new meaning later
```

- example 9. GraphQL and gRPC for vessels
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type vessels has id and name
- a query reads vs_7. a mutation writes name
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  name: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, name: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string name = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 9a  query item id vs_7 and ask only for name
# 9b  mutation setField id vs_7 value North Wind
# 9c  do not reuse protobuf field number 1 for a new meaning later
```

- example 10. GraphQL and gRPC for scores
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type scores has id and points
- a query reads sc_3. a mutation writes points
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  points: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, points: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string points = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 10a  query item id sc_3 and ask only for points
# 10b  mutation setField id sc_3 value 11
# 10c  do not reuse protobuf field number 1 for a new meaning later
```

- example 11. GraphQL and gRPC for teams
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type teams has id and name
- a query reads tm_2. a mutation writes name
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  name: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, name: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string name = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 11a  query item id tm_2 and ask only for name
# 11b  mutation setField id tm_2 value Blue
# 11c  do not reuse protobuf field number 1 for a new meaning later
```

- example 12. GraphQL and gRPC for uploads
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type uploads has id and key
- a query reads up_6. a mutation writes key
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  key: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, key: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string key = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 12a  query item id up_6 and ask only for key
# 12b  mutation setField id up_6 value videos/u_1/a.mp4
# 12c  do not reuse protobuf field number 1 for a new meaning later
```

- example 13. GraphQL and gRPC for standings
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type standings has id and player
- a query reads st_1. a mutation writes player
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  player: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, player: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string player = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 13a  query item id st_1 and ask only for player
# 13b  mutation setField id st_1 value player_a
# 13c  do not reuse protobuf field number 1 for a new meaning later
```

- example 14. GraphQL and gRPC for notices
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type notices has id and port
- a query reads n_2. a mutation writes port
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  port: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, port: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string port = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 14a  query item id n_2 and ask only for port
# 14b  mutation setField id n_2 value Chattogram
# 14c  do not reuse protobuf field number 1 for a new meaning later
```

- example 15. GraphQL and gRPC for brackets
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type brackets has id and round
- a query reads b_3. a mutation writes round
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  round: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, round: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string round = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 15a  query item id b_3 and ask only for round
# 15b  mutation setField id b_3 value semi
# 15c  do not reuse protobuf field number 1 for a new meaning later
```

- example 16. GraphQL and gRPC for sessions
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type sessions has id and userId
- a query reads se_1. a mutation writes userId
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  userId: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, userId: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string userId = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 16a  query item id se_1 and ask only for userId
# 16b  mutation setField id se_1 value u_3
# 16c  do not reuse protobuf field number 1 for a new meaning later
```

- example 17. GraphQL and gRPC for queues
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type queues has id and name
- a query reads q_1. a mutation writes name
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  name: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, name: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string name = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 17a  query item id q_1 and ask only for name
# 17b  mutation setField id q_1 value laytime
# 17c  do not reuse protobuf field number 1 for a new meaning later
```

- example 18. GraphQL and gRPC for roles
- the projects speak HTTP and JSON. this example is so the picture's two topics are fully written out
- GraphQL type roles has id and name
- a query reads r_1. a mutation writes name
- gRPC puts the same two fields in a message. field numbers stay forever

```graphql
type Item {
  id: ID!
  name: String!
}
type Query {
  item(id: ID!): Item
}
type Mutation {
  setField(id: ID!, name: String!): Item
}
```

```protobuf
message Item {
  string id = 1;
  string name = 2;
}
service Api {
  rpc Get(Item) returns (Item);
}
```

- homework
```graphql
# 18a  query item id r_1 and ask only for name
# 18b  mutation setField id r_1 value operator
# 18c  do not reuse protobuf field number 1 for a new meaning later
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

- example 1. a Nest module for port-calls
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(pc_12) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { vesselName: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 1a  one("pc_12")
// 1b  create({ vesselName: "Ocean Star" })
// 1c  the test passes a fake db, not a live Postgres
// 1d  name the guard that would sit in front of create
```

- example 2. a Nest module for matches
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(m_1) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { home: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 2a  one("m_1")
// 2b  create({ home: "Ada" })
// 2c  the test passes a fake db, not a live Postgres
// 2d  name the guard that would sit in front of create
```

- example 3. a Nest module for tournaments
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(t_9) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { name: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 3a  one("t_9")
// 3b  create({ name: "Rajshahi Open" })
// 3c  the test passes a fake db, not a live Postgres
// 3d  name the guard that would sit in front of create
```

- example 4. a Nest module for videos
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(v_4) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { title: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 4a  one("v_4")
// 4b  create({ title: "Episode 1" })
// 4c  the test passes a fake db, not a live Postgres
// 4d  name the guard that would sit in front of create
```

- example 5. a Nest module for facts
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(f_2) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { kind: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 5a  one("f_2")
// 5b  create({ kind: "NOR" })
// 5c  the test passes a fake db, not a live Postgres
// 5d  name the guard that would sit in front of create
```

- example 6. a Nest module for users
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(u_3) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { email: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 6a  one("u_3")
// 6b  create({ email: "a@example.com" })
// 6c  the test passes a fake db, not a live Postgres
// 6d  name the guard that would sit in front of create
```

- example 7. a Nest module for payments
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(pay_8) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { eventId: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 7a  one("pay_8")
// 7b  create({ eventId: "evt_1" })
// 7c  the test passes a fake db, not a live Postgres
// 7d  name the guard that would sit in front of create
```

- example 8. a Nest module for rooms
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(room_2) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { sessionId: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 8a  one("room_2")
// 8b  create({ sessionId: "s_9" })
// 8c  the test passes a fake db, not a live Postgres
// 8d  name the guard that would sit in front of create
```

- example 9. a Nest module for vessels
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(vs_7) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { name: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 9a  one("vs_7")
// 9b  create({ name: "North Wind" })
// 9c  the test passes a fake db, not a live Postgres
// 9d  name the guard that would sit in front of create
```

- example 10. a Nest module for scores
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(sc_3) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { points: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 10a  one("sc_3")
// 10b  create({ points: "11" })
// 10c  the test passes a fake db, not a live Postgres
// 10d  name the guard that would sit in front of create
```

- example 11. a Nest module for teams
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(tm_2) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { name: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 11a  one("tm_2")
// 11b  create({ name: "Blue" })
// 11c  the test passes a fake db, not a live Postgres
// 11d  name the guard that would sit in front of create
```

- example 12. a Nest module for uploads
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(up_6) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { key: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 12a  one("up_6")
// 12b  create({ key: "videos/u_1/a.mp4" })
// 12c  the test passes a fake db, not a live Postgres
// 12d  name the guard that would sit in front of create
```

- example 13. a Nest module for standings
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(st_1) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { player: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 13a  one("st_1")
// 13b  create({ player: "player_a" })
// 13c  the test passes a fake db, not a live Postgres
// 13d  name the guard that would sit in front of create
```

- example 14. a Nest module for notices
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(n_2) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { port: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 14a  one("n_2")
// 14b  create({ port: "Chattogram" })
// 14c  the test passes a fake db, not a live Postgres
// 14d  name the guard that would sit in front of create
```

- example 15. a Nest module for brackets
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(b_3) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { round: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 15a  one("b_3")
// 15b  create({ round: "semi" })
// 15c  the test passes a fake db, not a live Postgres
// 15d  name the guard that would sit in front of create
```

- example 16. a Nest module for sessions
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(se_1) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { userId: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 16a  one("se_1")
// 16b  create({ userId: "u_3" })
// 16c  the test passes a fake db, not a live Postgres
// 16d  name the guard that would sit in front of create
```

- example 17. a Nest module for queues
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(q_1) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { name: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 17a  one("q_1")
// 17b  create({ name: "laytime" })
// 17c  the test passes a fake db, not a live Postgres
// 17d  name the guard that would sit in front of create
```

- example 18. a Nest module for roles
- module lists the controller and the service
- the service asks for Prisma in the constructor
- we do not write new PrismaClient() inside the method
- one(r_1) uses findUniqueOrThrow

```ts
@Module({
  controllers: [ItemController],
  providers: [ItemService],
})
export class ItemModule {}

@Injectable()
export class ItemService {
  constructor(private readonly db: PrismaService) {}
  one(id: string) {
    return this.db.item.findUniqueOrThrow({ where: { id } });
  }
  create(data: { name: string }) {
    return this.db.item.create({ data });
  }
}
```

- homework
```ts
// 18a  one("r_1")
// 18b  create({ name: "operator" })
// 18c  the test passes a fake db, not a live Postgres
// 18d  name the guard that would sit in front of create
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

- example 1. login and tokens, user pc_12
- password for this example is never stored. bcrypt cost 12
- access token lives about 72 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = pc_12 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "pc_12", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 1a  what is stored for the password of pc_12
// 1b  what is stored for the refresh token
// 1c  sub in the access token is pc_12
// 1d  reuse of an old refresh token revokes the family
```

- example 2. login and tokens, user m_1
- password for this example is never stored. bcrypt cost 12
- access token lives about 3 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = m_1 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "m_1", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 2a  what is stored for the password of m_1
// 2b  what is stored for the refresh token
// 2c  sub in the access token is m_1
// 2d  reuse of an old refresh token revokes the family
```

- example 3. login and tokens, user t_9
- password for this example is never stored. bcrypt cost 12
- access token lives about 16 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = t_9 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "t_9", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 3a  what is stored for the password of t_9
// 3b  what is stored for the refresh token
// 3c  sub in the access token is t_9
// 3d  reuse of an old refresh token revokes the family
```

- example 4. login and tokens, user v_4
- password for this example is never stored. bcrypt cost 12
- access token lives about 6 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = v_4 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "v_4", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 4a  what is stored for the password of v_4
// 4b  what is stored for the refresh token
// 4c  sub in the access token is v_4
// 4d  reuse of an old refresh token revokes the family
```

- example 5. login and tokens, user f_2
- password for this example is never stored. bcrypt cost 12
- access token lives about 1 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = f_2 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "f_2", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 5a  what is stored for the password of f_2
// 5b  what is stored for the refresh token
// 5c  sub in the access token is f_2
// 5d  reuse of an old refresh token revokes the family
```

- example 6. login and tokens, user u_3
- password for this example is never stored. bcrypt cost 12
- access token lives about 1 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = u_3 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "u_3", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 6a  what is stored for the password of u_3
// 6b  what is stored for the refresh token
// 6c  sub in the access token is u_3
// 6d  reuse of an old refresh token revokes the family
```

- example 7. login and tokens, user pay_8
- password for this example is never stored. bcrypt cost 12
- access token lives about 15000 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = pay_8 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "pay_8", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 7a  what is stored for the password of pay_8
// 7b  what is stored for the refresh token
// 7c  sub in the access token is pay_8
// 7d  reuse of an old refresh token revokes the family
```

- example 8. login and tokens, user room_2
- password for this example is never stored. bcrypt cost 12
- access token lives about 12 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = room_2 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "room_2", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 8a  what is stored for the password of room_2
// 8b  what is stored for the refresh token
// 8c  sub in the access token is room_2
// 8d  reuse of an old refresh token revokes the family
```

- example 9. login and tokens, user vs_7
- password for this example is never stored. bcrypt cost 12
- access token lives about 48 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = vs_7 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "vs_7", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 9a  what is stored for the password of vs_7
// 9b  what is stored for the refresh token
// 9c  sub in the access token is vs_7
// 9d  reuse of an old refresh token revokes the family
```

- example 10. login and tokens, user sc_3
- password for this example is never stored. bcrypt cost 12
- access token lives about 11 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = sc_3 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "sc_3", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 10a  what is stored for the password of sc_3
// 10b  what is stored for the refresh token
// 10c  sub in the access token is sc_3
// 10d  reuse of an old refresh token revokes the family
```

- example 11. login and tokens, user tm_2
- password for this example is never stored. bcrypt cost 12
- access token lives about 2 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = tm_2 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "tm_2", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 11a  what is stored for the password of tm_2
// 11b  what is stored for the refresh token
// 11c  sub in the access token is tm_2
// 11d  reuse of an old refresh token revokes the family
```

- example 12. login and tokens, user up_6
- password for this example is never stored. bcrypt cost 12
- access token lives about 200 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = up_6 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "up_6", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 12a  what is stored for the password of up_6
// 12b  what is stored for the refresh token
// 12c  sub in the access token is up_6
// 12d  reuse of an old refresh token revokes the family
```

- example 13. login and tokens, user st_1
- password for this example is never stored. bcrypt cost 12
- access token lives about 1500 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = st_1 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "st_1", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 13a  what is stored for the password of st_1
// 13b  what is stored for the refresh token
// 13c  sub in the access token is st_1
// 13d  reuse of an old refresh token revokes the family
```

- example 14. login and tokens, user n_2
- password for this example is never stored. bcrypt cost 12
- access token lives about 24 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = n_2 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "n_2", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 14a  what is stored for the password of n_2
// 14b  what is stored for the refresh token
// 14c  sub in the access token is n_2
// 14d  reuse of an old refresh token revokes the family
```

- example 15. login and tokens, user b_3
- password for this example is never stored. bcrypt cost 12
- access token lives about 4 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = b_3 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "b_3", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 15a  what is stored for the password of b_3
// 15b  what is stored for the refresh token
// 15c  sub in the access token is b_3
// 15d  reuse of an old refresh token revokes the family
```

- example 16. login and tokens, user se_1
- password for this example is never stored. bcrypt cost 12
- access token lives about 15 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = se_1 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "se_1", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 16a  what is stored for the password of se_1
// 16b  what is stored for the refresh token
// 16c  sub in the access token is se_1
// 16d  reuse of an old refresh token revokes the family
```

- example 17. login and tokens, user q_1
- password for this example is never stored. bcrypt cost 12
- access token lives about 10 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = q_1 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "q_1", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 17a  what is stored for the password of q_1
// 17b  what is stored for the refresh token
// 17c  sub in the access token is q_1
// 17d  reuse of an old refresh token revokes the family
```

- example 18. login and tokens, user r_1
- password for this example is never stored. bcrypt cost 12
- access token lives about 2 minutes in the earlier number, we keep real tokens near 15 minutes
- claims are sub = r_1 and the roles. no password in the payload
- refresh token is stored as a hash and rotated

```ts
const passwordHash = await hash(plain, 12);
const ok = await compare(plain, passwordHash);
const access = { sub: "r_1", roles: ["operator"], exp: now + 15 * 60 };
// refresh row: hash only, family id, replaced on every refresh
```

- a JWT can be read by anyone who holds it. the signature stops edits
- homework
```ts
// 18a  what is stored for the password of r_1
// 18b  what is stored for the refresh token
// 18c  sub in the access token is r_1
// 18d  reuse of an old refresh token revokes the family
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

- example 1. cookie and bearer for port-calls
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for pc_12>
Set-Cookie: sid=pc_12; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 1a  three cookie flags
// 1b  why POST from another site does not include the bearer token
// 1c  where the OAuth secret for port-calls would live
// 1d  add the CSRF token in the same change as the cookie
```

- example 2. cookie and bearer for matches
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for m_1>
Set-Cookie: sid=m_1; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 2a  three cookie flags
// 2b  why POST from another site does not include the bearer token
// 2c  where the OAuth secret for matches would live
// 2d  add the CSRF token in the same change as the cookie
```

- example 3. cookie and bearer for tournaments
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for t_9>
Set-Cookie: sid=t_9; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 3a  three cookie flags
// 3b  why POST from another site does not include the bearer token
// 3c  where the OAuth secret for tournaments would live
// 3d  add the CSRF token in the same change as the cookie
```

- example 4. cookie and bearer for videos
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for v_4>
Set-Cookie: sid=v_4; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 4a  three cookie flags
// 4b  why POST from another site does not include the bearer token
// 4c  where the OAuth secret for videos would live
// 4d  add the CSRF token in the same change as the cookie
```

- example 5. cookie and bearer for facts
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for f_2>
Set-Cookie: sid=f_2; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 5a  three cookie flags
// 5b  why POST from another site does not include the bearer token
// 5c  where the OAuth secret for facts would live
// 5d  add the CSRF token in the same change as the cookie
```

- example 6. cookie and bearer for users
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for u_3>
Set-Cookie: sid=u_3; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 6a  three cookie flags
// 6b  why POST from another site does not include the bearer token
// 6c  where the OAuth secret for users would live
// 6d  add the CSRF token in the same change as the cookie
```

- example 7. cookie and bearer for payments
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for pay_8>
Set-Cookie: sid=pay_8; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 7a  three cookie flags
// 7b  why POST from another site does not include the bearer token
// 7c  where the OAuth secret for payments would live
// 7d  add the CSRF token in the same change as the cookie
```

- example 8. cookie and bearer for rooms
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for room_2>
Set-Cookie: sid=room_2; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 8a  three cookie flags
// 8b  why POST from another site does not include the bearer token
// 8c  where the OAuth secret for rooms would live
// 8d  add the CSRF token in the same change as the cookie
```

- example 9. cookie and bearer for vessels
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for vs_7>
Set-Cookie: sid=vs_7; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 9a  three cookie flags
// 9b  why POST from another site does not include the bearer token
// 9c  where the OAuth secret for vessels would live
// 9d  add the CSRF token in the same change as the cookie
```

- example 10. cookie and bearer for scores
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for sc_3>
Set-Cookie: sid=sc_3; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 10a  three cookie flags
// 10b  why POST from another site does not include the bearer token
// 10c  where the OAuth secret for scores would live
// 10d  add the CSRF token in the same change as the cookie
```

- example 11. cookie and bearer for teams
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for tm_2>
Set-Cookie: sid=tm_2; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 11a  three cookie flags
// 11b  why POST from another site does not include the bearer token
// 11c  where the OAuth secret for teams would live
// 11d  add the CSRF token in the same change as the cookie
```

- example 12. cookie and bearer for uploads
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for up_6>
Set-Cookie: sid=up_6; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 12a  three cookie flags
// 12b  why POST from another site does not include the bearer token
// 12c  where the OAuth secret for uploads would live
// 12d  add the CSRF token in the same change as the cookie
```

- example 13. cookie and bearer for standings
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for st_1>
Set-Cookie: sid=st_1; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 13a  three cookie flags
// 13b  why POST from another site does not include the bearer token
// 13c  where the OAuth secret for standings would live
// 13d  add the CSRF token in the same change as the cookie
```

- example 14. cookie and bearer for notices
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for n_2>
Set-Cookie: sid=n_2; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 14a  three cookie flags
// 14b  why POST from another site does not include the bearer token
// 14c  where the OAuth secret for notices would live
// 14d  add the CSRF token in the same change as the cookie
```

- example 15. cookie and bearer for brackets
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for b_3>
Set-Cookie: sid=b_3; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 15a  three cookie flags
// 15b  why POST from another site does not include the bearer token
// 15c  where the OAuth secret for brackets would live
// 15d  add the CSRF token in the same change as the cookie
```

- example 16. cookie and bearer for sessions
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for se_1>
Set-Cookie: sid=se_1; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 16a  three cookie flags
// 16b  why POST from another site does not include the bearer token
// 16c  where the OAuth secret for sessions would live
// 16d  add the CSRF token in the same change as the cookie
```

- example 17. cookie and bearer for queues
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for q_1>
Set-Cookie: sid=q_1; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 17a  three cookie flags
// 17b  why POST from another site does not include the bearer token
// 17c  where the OAuth secret for queues would live
// 17d  add the CSRF token in the same change as the cookie
```

- example 18. cookie and bearer for roles
- our apps use a bearer access token. a cookie session is the other design
- HttpOnly, Secure, SameSite=Lax
- a foreign form does not attach an Authorization header
- OAuth client secret stays on the server. the redirect URI is an allowlist
- state is a random value we check when the browser comes back

```http
Authorization: Bearer <access for r_1>
Set-Cookie: sid=r_1; HttpOnly; Secure; SameSite=Lax
```

- homework
```ts
// 18a  three cookie flags
// 18b  why POST from another site does not include the bearer token
// 18c  where the OAuth secret for roles would live
// 18d  add the CSRF token in the same change as the cookie
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

- example 1. Redis key login:pc_12
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when vesselName changes, delete the cache key

```ts
const key = "login:pc_12";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("port-calls:pc_12", "Ocean Star", "EX", 60);
```

- homework
```ts
// 1a  key login:pc_12 hit 1 sets the TTL
// 1b  hit 11 throws 429
// 1c  flushing Redis loses the counter and keeps Postgres
// 1d  Anemi has no redis.incr
```

- example 2. Redis key login:m_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when home changes, delete the cache key

```ts
const key = "login:m_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("matches:m_1", "Ada", "EX", 60);
```

- homework
```ts
// 2a  key login:m_1 hit 1 sets the TTL
// 2b  hit 11 throws 429
// 2c  flushing Redis loses the counter and keeps Postgres
// 2d  Anemi has no redis.incr
```

- example 3. Redis key login:t_9
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:t_9";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("tournaments:t_9", "Rajshahi Open", "EX", 60);
```

- homework
```ts
// 3a  key login:t_9 hit 1 sets the TTL
// 3b  hit 11 throws 429
// 3c  flushing Redis loses the counter and keeps Postgres
// 3d  Anemi has no redis.incr
```

- example 4. Redis key login:v_4
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when title changes, delete the cache key

```ts
const key = "login:v_4";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("videos:v_4", "Episode 1", "EX", 60);
```

- homework
```ts
// 4a  key login:v_4 hit 1 sets the TTL
// 4b  hit 11 throws 429
// 4c  flushing Redis loses the counter and keeps Postgres
// 4d  Anemi has no redis.incr
```

- example 5. Redis key login:f_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when kind changes, delete the cache key

```ts
const key = "login:f_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("facts:f_2", "NOR", "EX", 60);
```

- homework
```ts
// 5a  key login:f_2 hit 1 sets the TTL
// 5b  hit 11 throws 429
// 5c  flushing Redis loses the counter and keeps Postgres
// 5d  Anemi has no redis.incr
```

- example 6. Redis key login:u_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when email changes, delete the cache key

```ts
const key = "login:u_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("users:u_3", "a@example.com", "EX", 60);
```

- homework
```ts
// 6a  key login:u_3 hit 1 sets the TTL
// 6b  hit 11 throws 429
// 6c  flushing Redis loses the counter and keeps Postgres
// 6d  Anemi has no redis.incr
```

- example 7. Redis key login:pay_8
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when eventId changes, delete the cache key

```ts
const key = "login:pay_8";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("payments:pay_8", "evt_1", "EX", 60);
```

- homework
```ts
// 7a  key login:pay_8 hit 1 sets the TTL
// 7b  hit 11 throws 429
// 7c  flushing Redis loses the counter and keeps Postgres
// 7d  Anemi has no redis.incr
```

- example 8. Redis key login:room_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when sessionId changes, delete the cache key

```ts
const key = "login:room_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("rooms:room_2", "s_9", "EX", 60);
```

- homework
```ts
// 8a  key login:room_2 hit 1 sets the TTL
// 8b  hit 11 throws 429
// 8c  flushing Redis loses the counter and keeps Postgres
// 8d  Anemi has no redis.incr
```

- example 9. Redis key login:vs_7
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:vs_7";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("vessels:vs_7", "North Wind", "EX", 60);
```

- homework
```ts
// 9a  key login:vs_7 hit 1 sets the TTL
// 9b  hit 11 throws 429
// 9c  flushing Redis loses the counter and keeps Postgres
// 9d  Anemi has no redis.incr
```

- example 10. Redis key login:sc_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when points changes, delete the cache key

```ts
const key = "login:sc_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("scores:sc_3", "11", "EX", 60);
```

- homework
```ts
// 10a  key login:sc_3 hit 1 sets the TTL
// 10b  hit 11 throws 429
// 10c  flushing Redis loses the counter and keeps Postgres
// 10d  Anemi has no redis.incr
```

- example 11. Redis key login:tm_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:tm_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("teams:tm_2", "Blue", "EX", 60);
```

- homework
```ts
// 11a  key login:tm_2 hit 1 sets the TTL
// 11b  hit 11 throws 429
// 11c  flushing Redis loses the counter and keeps Postgres
// 11d  Anemi has no redis.incr
```

- example 12. Redis key login:up_6
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when key changes, delete the cache key

```ts
const key = "login:up_6";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("uploads:up_6", "videos/u_1/a.mp4", "EX", 60);
```

- homework
```ts
// 12a  key login:up_6 hit 1 sets the TTL
// 12b  hit 11 throws 429
// 12c  flushing Redis loses the counter and keeps Postgres
// 12d  Anemi has no redis.incr
```

- example 13. Redis key login:st_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when player changes, delete the cache key

```ts
const key = "login:st_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("standings:st_1", "player_a", "EX", 60);
```

- homework
```ts
// 13a  key login:st_1 hit 1 sets the TTL
// 13b  hit 11 throws 429
// 13c  flushing Redis loses the counter and keeps Postgres
// 13d  Anemi has no redis.incr
```

- example 14. Redis key login:n_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when port changes, delete the cache key

```ts
const key = "login:n_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("notices:n_2", "Chattogram", "EX", 60);
```

- homework
```ts
// 14a  key login:n_2 hit 1 sets the TTL
// 14b  hit 11 throws 429
// 14c  flushing Redis loses the counter and keeps Postgres
// 14d  Anemi has no redis.incr
```

- example 15. Redis key login:b_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when round changes, delete the cache key

```ts
const key = "login:b_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("brackets:b_3", "semi", "EX", 60);
```

- homework
```ts
// 15a  key login:b_3 hit 1 sets the TTL
// 15b  hit 11 throws 429
// 15c  flushing Redis loses the counter and keeps Postgres
// 15d  Anemi has no redis.incr
```

- example 16. Redis key login:se_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when userId changes, delete the cache key

```ts
const key = "login:se_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("sessions:se_1", "u_3", "EX", 60);
```

- homework
```ts
// 16a  key login:se_1 hit 1 sets the TTL
// 16b  hit 11 throws 429
// 16c  flushing Redis loses the counter and keeps Postgres
// 16d  Anemi has no redis.incr
```

- example 17. Redis key login:q_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:q_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("queues:q_1", "laytime", "EX", 60);
```

- homework
```ts
// 17a  key login:q_1 hit 1 sets the TTL
// 17b  hit 11 throws 429
// 17c  flushing Redis loses the counter and keeps Postgres
// 17d  Anemi has no redis.incr
```

- example 18. Redis key login:r_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:r_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("roles:r_1", "operator", "EX", 60);
```

- homework
```ts
// 18a  key login:r_1 hit 1 sets the TTL
// 18b  hit 11 throws 429
// 18c  flushing Redis loses the counter and keeps Postgres
// 18d  Anemi has no redis.incr
```

- example 19. Redis key login:pc_12
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when vesselName changes, delete the cache key

```ts
const key = "login:pc_12";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("port-calls:pc_12", "Ocean Star", "EX", 60);
```

- homework
```ts
// 19a  key login:pc_12 hit 1 sets the TTL
// 19b  hit 11 throws 429
// 19c  flushing Redis loses the counter and keeps Postgres
// 19d  Anemi has no redis.incr
```

- example 20. Redis key login:m_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when home changes, delete the cache key

```ts
const key = "login:m_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("matches:m_1", "Ada", "EX", 60);
```

- homework
```ts
// 20a  key login:m_1 hit 1 sets the TTL
// 20b  hit 11 throws 429
// 20c  flushing Redis loses the counter and keeps Postgres
// 20d  Anemi has no redis.incr
```

- example 21. Redis key login:t_9
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:t_9";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("tournaments:t_9", "Rajshahi Open", "EX", 60);
```

- homework
```ts
// 21a  key login:t_9 hit 1 sets the TTL
// 21b  hit 11 throws 429
// 21c  flushing Redis loses the counter and keeps Postgres
// 21d  Anemi has no redis.incr
```

- example 22. Redis key login:v_4
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when title changes, delete the cache key

```ts
const key = "login:v_4";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("videos:v_4", "Episode 1", "EX", 60);
```

- homework
```ts
// 22a  key login:v_4 hit 1 sets the TTL
// 22b  hit 11 throws 429
// 22c  flushing Redis loses the counter and keeps Postgres
// 22d  Anemi has no redis.incr
```

- example 23. Redis key login:f_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when kind changes, delete the cache key

```ts
const key = "login:f_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("facts:f_2", "NOR", "EX", 60);
```

- homework
```ts
// 23a  key login:f_2 hit 1 sets the TTL
// 23b  hit 11 throws 429
// 23c  flushing Redis loses the counter and keeps Postgres
// 23d  Anemi has no redis.incr
```

- example 24. Redis key login:u_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when email changes, delete the cache key

```ts
const key = "login:u_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("users:u_3", "a@example.com", "EX", 60);
```

- homework
```ts
// 24a  key login:u_3 hit 1 sets the TTL
// 24b  hit 11 throws 429
// 24c  flushing Redis loses the counter and keeps Postgres
// 24d  Anemi has no redis.incr
```

- example 25. Redis key login:pay_8
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when eventId changes, delete the cache key

```ts
const key = "login:pay_8";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("payments:pay_8", "evt_1", "EX", 60);
```

- homework
```ts
// 25a  key login:pay_8 hit 1 sets the TTL
// 25b  hit 11 throws 429
// 25c  flushing Redis loses the counter and keeps Postgres
// 25d  Anemi has no redis.incr
```

- example 26. Redis key login:room_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when sessionId changes, delete the cache key

```ts
const key = "login:room_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("rooms:room_2", "s_9", "EX", 60);
```

- homework
```ts
// 26a  key login:room_2 hit 1 sets the TTL
// 26b  hit 11 throws 429
// 26c  flushing Redis loses the counter and keeps Postgres
// 26d  Anemi has no redis.incr
```

- example 27. Redis key login:vs_7
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:vs_7";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("vessels:vs_7", "North Wind", "EX", 60);
```

- homework
```ts
// 27a  key login:vs_7 hit 1 sets the TTL
// 27b  hit 11 throws 429
// 27c  flushing Redis loses the counter and keeps Postgres
// 27d  Anemi has no redis.incr
```

- example 28. Redis key login:sc_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when points changes, delete the cache key

```ts
const key = "login:sc_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("scores:sc_3", "11", "EX", 60);
```

- homework
```ts
// 28a  key login:sc_3 hit 1 sets the TTL
// 28b  hit 11 throws 429
// 28c  flushing Redis loses the counter and keeps Postgres
// 28d  Anemi has no redis.incr
```

- example 29. Redis key login:tm_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:tm_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("teams:tm_2", "Blue", "EX", 60);
```

- homework
```ts
// 29a  key login:tm_2 hit 1 sets the TTL
// 29b  hit 11 throws 429
// 29c  flushing Redis loses the counter and keeps Postgres
// 29d  Anemi has no redis.incr
```

- example 30. Redis key login:up_6
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when key changes, delete the cache key

```ts
const key = "login:up_6";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("uploads:up_6", "videos/u_1/a.mp4", "EX", 60);
```

- homework
```ts
// 30a  key login:up_6 hit 1 sets the TTL
// 30b  hit 11 throws 429
// 30c  flushing Redis loses the counter and keeps Postgres
// 30d  Anemi has no redis.incr
```

- example 31. Redis key login:st_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when player changes, delete the cache key

```ts
const key = "login:st_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("standings:st_1", "player_a", "EX", 60);
```

- homework
```ts
// 31a  key login:st_1 hit 1 sets the TTL
// 31b  hit 11 throws 429
// 31c  flushing Redis loses the counter and keeps Postgres
// 31d  Anemi has no redis.incr
```

- example 32. Redis key login:n_2
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when port changes, delete the cache key

```ts
const key = "login:n_2";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("notices:n_2", "Chattogram", "EX", 60);
```

- homework
```ts
// 32a  key login:n_2 hit 1 sets the TTL
// 32b  hit 11 throws 429
// 32c  flushing Redis loses the counter and keeps Postgres
// 32d  Anemi has no redis.incr
```

- example 33. Redis key login:b_3
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when round changes, delete the cache key

```ts
const key = "login:b_3";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("brackets:b_3", "semi", "EX", 60);
```

- homework
```ts
// 33a  key login:b_3 hit 1 sets the TTL
// 33b  hit 11 throws 429
// 33c  flushing Redis loses the counter and keeps Postgres
// 33d  Anemi has no redis.incr
```

- example 34. Redis key login:se_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when userId changes, delete the cache key

```ts
const key = "login:se_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("sessions:se_1", "u_3", "EX", 60);
```

- homework
```ts
// 34a  key login:se_1 hit 1 sets the TTL
// 34b  hit 11 throws 429
// 34c  flushing Redis loses the counter and keeps Postgres
// 34d  Anemi has no redis.incr
```

- example 35. Redis key login:q_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:q_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("queues:q_1", "laytime", "EX", 60);
```

- homework
```ts
// 35a  key login:q_1 hit 1 sets the TTL
// 35b  hit 11 throws 429
// 35c  flushing Redis loses the counter and keeps Postgres
// 35d  Anemi has no redis.incr
```

- example 36. Redis key login:r_1
- INCR then EXPIRE on the first hit
- more than 10 hits in 60 seconds throws 429
- VMS and Bracket use Redis for this counter and for BullMQ
- Anemi does not use Redis
- a cache key for a name is separate from the counter key
- when name changes, delete the cache key

```ts
const key = "login:r_1";
const hits = await redis.incr(key);
if (hits === 1) await redis.expire(key, 60);
if (hits > 10) throw new HttpException("Too many attempts", 429);
await redis.set("roles:r_1", "operator", "EX", 60);
```

- homework
```ts
// 36a  key login:r_1 hit 1 sets the TTL
// 36b  hit 11 throws 429
// 36c  flushing Redis loses the counter and keeps Postgres
// 36d  Anemi has no redis.incr
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

- example 1. numbers for a laytime-shaped job
- allowed 72, used 84, rate 15000 a day
- demurrage 7500.0, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:pc_12
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "pc_12" }, { jobId: "laytime:pc_12" });
laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 });
// demurrage 7500.0, despatch 0
```

- homework
```ts
// 1a  recompute allowed 72, used 84, rate 15000
// 1b  demurrage is 7500.0
// 1c  job id laytime:pc_12
// 1d  running the pure function twice on the same inputs returns the same money
```

- example 2. numbers for a laytime-shaped job
- allowed 3, used 1, rate 32 a day
- demurrage 0, despatch 1.33
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:m_1
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "m_1" }, { jobId: "laytime:m_1" });
laytime({ allowedHours: 3, usedHours: 1, demurragePerDay: 32 });
// demurrage 0, despatch 1.33
```

- homework
```ts
// 2a  recompute allowed 3, used 1, rate 32
// 2b  demurrage is 0
// 2c  job id laytime:m_1
// 2d  running the pure function twice on the same inputs returns the same money
```

- example 3. numbers for a laytime-shaped job
- allowed 16, used 8, rate 24 a day
- demurrage 0, despatch 4.0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:t_9
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "t_9" }, { jobId: "laytime:t_9" });
laytime({ allowedHours: 16, usedHours: 8, demurragePerDay: 24 });
// demurrage 0, despatch 4.0
```

- homework
```ts
// 3a  recompute allowed 16, used 8, rate 24
// 3b  demurrage is 0
// 3c  job id laytime:t_9
// 3d  running the pure function twice on the same inputs returns the same money
```

- example 4. numbers for a laytime-shaped job
- allowed 6, used 10, rate 6 a day
- demurrage 1.0, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:v_4
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "v_4" }, { jobId: "laytime:v_4" });
laytime({ allowedHours: 6, usedHours: 10, demurragePerDay: 6 });
// demurrage 1.0, despatch 0
```

- homework
```ts
// 4a  recompute allowed 6, used 10, rate 6
// 4b  demurrage is 1.0
// 4c  job id laytime:v_4
// 4d  running the pure function twice on the same inputs returns the same money
```

- example 5. numbers for a laytime-shaped job
- allowed 1, used 2, rate 500 a day
- demurrage 20.83, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:f_2
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "f_2" }, { jobId: "laytime:f_2" });
laytime({ allowedHours: 1, usedHours: 2, demurragePerDay: 500 });
// demurrage 20.83, despatch 0
```

- homework
```ts
// 5a  recompute allowed 1, used 2, rate 500
// 5b  demurrage is 20.83
// 5c  job id laytime:f_2
// 5d  running the pure function twice on the same inputs returns the same money
```

- example 6. numbers for a laytime-shaped job
- allowed 1, used 1, rate 12 a day
- demurrage 0, despatch 0.0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:u_3
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "u_3" }, { jobId: "laytime:u_3" });
laytime({ allowedHours: 1, usedHours: 1, demurragePerDay: 12 });
// demurrage 0, despatch 0.0
```

- homework
```ts
// 6a  recompute allowed 1, used 1, rate 12
// 6b  demurrage is 0
// 6c  job id laytime:u_3
// 6d  running the pure function twice on the same inputs returns the same money
```

- example 7. numbers for a laytime-shaped job
- allowed 15000, used 7500, rate 1 a day
- demurrage 0, despatch 156.25
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:pay_8
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "pay_8" }, { jobId: "laytime:pay_8" });
laytime({ allowedHours: 15000, usedHours: 7500, demurragePerDay: 1 });
// demurrage 0, despatch 156.25
```

- homework
```ts
// 7a  recompute allowed 15000, used 7500, rate 1
// 7b  demurrage is 0
// 7c  job id laytime:pay_8
// 7d  running the pure function twice on the same inputs returns the same money
```

- example 8. numbers for a laytime-shaped job
- allowed 12, used 40, rate 1 a day
- demurrage 1.17, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:room_2
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "room_2" }, { jobId: "laytime:room_2" });
laytime({ allowedHours: 12, usedHours: 40, demurragePerDay: 1 });
// demurrage 1.17, despatch 0
```

- homework
```ts
// 8a  recompute allowed 12, used 40, rate 1
// 8b  demurrage is 1.17
// 8c  job id laytime:room_2
// 8d  running the pure function twice on the same inputs returns the same money
```

- example 9. numbers for a laytime-shaped job
- allowed 48, used 60, rate 8000 a day
- demurrage 4000.0, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:vs_7
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "vs_7" }, { jobId: "laytime:vs_7" });
laytime({ allowedHours: 48, usedHours: 60, demurragePerDay: 8000 });
// demurrage 4000.0, despatch 0
```

- homework
```ts
// 9a  recompute allowed 48, used 60, rate 8000
// 9b  demurrage is 4000.0
// 9c  job id laytime:vs_7
// 9d  running the pure function twice on the same inputs returns the same money
```

- example 10. numbers for a laytime-shaped job
- allowed 11, used 7, rate 32 a day
- demurrage 0, despatch 2.67
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:sc_3
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "sc_3" }, { jobId: "laytime:sc_3" });
laytime({ allowedHours: 11, usedHours: 7, demurragePerDay: 32 });
// demurrage 0, despatch 2.67
```

- homework
```ts
// 10a  recompute allowed 11, used 7, rate 32
// 10b  demurrage is 0
// 10c  job id laytime:sc_3
// 10d  running the pure function twice on the same inputs returns the same money
```

- example 11. numbers for a laytime-shaped job
- allowed 2, used 1, rate 10 a day
- demurrage 0, despatch 0.21
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:tm_2
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "tm_2" }, { jobId: "laytime:tm_2" });
laytime({ allowedHours: 2, usedHours: 1, demurragePerDay: 10 });
// demurrage 0, despatch 0.21
```

- homework
```ts
// 11a  recompute allowed 2, used 1, rate 10
// 11b  demurrage is 0
// 11c  job id laytime:tm_2
// 11d  running the pure function twice on the same inputs returns the same money
```

- example 12. numbers for a laytime-shaped job
- allowed 200, used 6, rate 1 a day
- demurrage 0, despatch 4.04
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:up_6
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "up_6" }, { jobId: "laytime:up_6" });
laytime({ allowedHours: 200, usedHours: 6, demurragePerDay: 1 });
// demurrage 0, despatch 4.04
```

- homework
```ts
// 12a  recompute allowed 200, used 6, rate 1
// 12b  demurrage is 0
// 12c  job id laytime:up_6
// 12d  running the pure function twice on the same inputs returns the same money
```

- example 13. numbers for a laytime-shaped job
- allowed 1500, used 1700, rate 32 a day
- demurrage 266.67, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:st_1
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "st_1" }, { jobId: "laytime:st_1" });
laytime({ allowedHours: 1500, usedHours: 1700, demurragePerDay: 32 });
// demurrage 266.67, despatch 0
```

- homework
```ts
// 13a  recompute allowed 1500, used 1700, rate 32
// 13b  demurrage is 266.67
// 13c  job id laytime:st_1
// 13d  running the pure function twice on the same inputs returns the same money
```

- example 14. numbers for a laytime-shaped job
- allowed 24, used 30, rate 12000 a day
- demurrage 3000.0, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:n_2
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "n_2" }, { jobId: "laytime:n_2" });
laytime({ allowedHours: 24, usedHours: 30, demurragePerDay: 12000 });
// demurrage 3000.0, despatch 0
```

- homework
```ts
// 14a  recompute allowed 24, used 30, rate 12000
// 14b  demurrage is 3000.0
// 14c  job id laytime:n_2
// 14d  running the pure function twice on the same inputs returns the same money
```

- example 15. numbers for a laytime-shaped job
- allowed 4, used 2, rate 16 a day
- demurrage 0, despatch 0.67
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:b_3
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "b_3" }, { jobId: "laytime:b_3" });
laytime({ allowedHours: 4, usedHours: 2, demurragePerDay: 16 });
// demurrage 0, despatch 0.67
```

- homework
```ts
// 15a  recompute allowed 4, used 2, rate 16
// 15b  demurrage is 0
// 15c  job id laytime:b_3
// 15d  running the pure function twice on the same inputs returns the same money
```

- example 16. numbers for a laytime-shaped job
- allowed 15, used 60, rate 1 a day
- demurrage 1.88, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:se_1
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "se_1" }, { jobId: "laytime:se_1" });
laytime({ allowedHours: 15, usedHours: 60, demurragePerDay: 1 });
// demurrage 1.88, despatch 0
```

- homework
```ts
// 16a  recompute allowed 15, used 60, rate 1
// 16b  demurrage is 1.88
// 16c  job id laytime:se_1
// 16d  running the pure function twice on the same inputs returns the same money
```

- example 17. numbers for a laytime-shaped job
- allowed 10, used 3, rate 1 a day
- demurrage 0, despatch 0.15
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:q_1
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "q_1" }, { jobId: "laytime:q_1" });
laytime({ allowedHours: 10, usedHours: 3, demurragePerDay: 1 });
// demurrage 0, despatch 0.15
```

- homework
```ts
// 17a  recompute allowed 10, used 3, rate 1
// 17b  demurrage is 0
// 17c  job id laytime:q_1
// 17d  running the pure function twice on the same inputs returns the same money
```

- example 18. numbers for a laytime-shaped job
- allowed 2, used 4, rate 1 a day
- demurrage 0.08, despatch 0
- despatch at half rate is only an example clause. use it when the contract says so
- job id = laytime:r_1
- the same id does not run twice in parallel
- Anemi does not use this queue

```ts
await queue.add("recalculate", { portCallId: "r_1" }, { jobId: "laytime:r_1" });
laytime({ allowedHours: 2, usedHours: 4, demurragePerDay: 1 });
// demurrage 0.08, despatch 0
```

- homework
```ts
// 18a  recompute allowed 2, used 4, rate 1
// 18b  demurrage is 0.08
// 18c  job id laytime:r_1
// 18d  running the pure function twice on the same inputs returns the same money
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

- example 1. socket room for pc_12
- room name = watch:pc_12
- join on watch:join
- seek sends position 72 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:pc_12");
socket.to("watch:pc_12").emit("playback", {
  position: 72,
  serverTime: Date.now(),
});
```

- homework
```ts
// 1a  room for pc_12
// 1b  fields position and serverTime
// 1c  where the JWT is read
// 1d  remove the listener when the screen for pc_12 closes
```

- example 2. socket room for m_1
- room name = watch:m_1
- join on watch:join
- seek sends position 3 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:m_1");
socket.to("watch:m_1").emit("playback", {
  position: 3,
  serverTime: Date.now(),
});
```

- homework
```ts
// 2a  room for m_1
// 2b  fields position and serverTime
// 2c  where the JWT is read
// 2d  remove the listener when the screen for m_1 closes
```

- example 3. socket room for t_9
- room name = watch:t_9
- join on watch:join
- seek sends position 16 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:t_9");
socket.to("watch:t_9").emit("playback", {
  position: 16,
  serverTime: Date.now(),
});
```

- homework
```ts
// 3a  room for t_9
// 3b  fields position and serverTime
// 3c  where the JWT is read
// 3d  remove the listener when the screen for t_9 closes
```

- example 4. socket room for v_4
- room name = watch:v_4
- join on watch:join
- seek sends position 6 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:v_4");
socket.to("watch:v_4").emit("playback", {
  position: 6,
  serverTime: Date.now(),
});
```

- homework
```ts
// 4a  room for v_4
// 4b  fields position and serverTime
// 4c  where the JWT is read
// 4d  remove the listener when the screen for v_4 closes
```

- example 5. socket room for f_2
- room name = watch:f_2
- join on watch:join
- seek sends position 1 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:f_2");
socket.to("watch:f_2").emit("playback", {
  position: 1,
  serverTime: Date.now(),
});
```

- homework
```ts
// 5a  room for f_2
// 5b  fields position and serverTime
// 5c  where the JWT is read
// 5d  remove the listener when the screen for f_2 closes
```

- example 6. socket room for u_3
- room name = watch:u_3
- join on watch:join
- seek sends position 1 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:u_3");
socket.to("watch:u_3").emit("playback", {
  position: 1,
  serverTime: Date.now(),
});
```

- homework
```ts
// 6a  room for u_3
// 6b  fields position and serverTime
// 6c  where the JWT is read
// 6d  remove the listener when the screen for u_3 closes
```

- example 7. socket room for pay_8
- room name = watch:pay_8
- join on watch:join
- seek sends position 15000 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:pay_8");
socket.to("watch:pay_8").emit("playback", {
  position: 15000,
  serverTime: Date.now(),
});
```

- homework
```ts
// 7a  room for pay_8
// 7b  fields position and serverTime
// 7c  where the JWT is read
// 7d  remove the listener when the screen for pay_8 closes
```

- example 8. socket room for room_2
- room name = watch:room_2
- join on watch:join
- seek sends position 12 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:room_2");
socket.to("watch:room_2").emit("playback", {
  position: 12,
  serverTime: Date.now(),
});
```

- homework
```ts
// 8a  room for room_2
// 8b  fields position and serverTime
// 8c  where the JWT is read
// 8d  remove the listener when the screen for room_2 closes
```

- example 9. socket room for vs_7
- room name = watch:vs_7
- join on watch:join
- seek sends position 48 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:vs_7");
socket.to("watch:vs_7").emit("playback", {
  position: 48,
  serverTime: Date.now(),
});
```

- homework
```ts
// 9a  room for vs_7
// 9b  fields position and serverTime
// 9c  where the JWT is read
// 9d  remove the listener when the screen for vs_7 closes
```

- example 10. socket room for sc_3
- room name = watch:sc_3
- join on watch:join
- seek sends position 11 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:sc_3");
socket.to("watch:sc_3").emit("playback", {
  position: 11,
  serverTime: Date.now(),
});
```

- homework
```ts
// 10a  room for sc_3
// 10b  fields position and serverTime
// 10c  where the JWT is read
// 10d  remove the listener when the screen for sc_3 closes
```

- example 11. socket room for tm_2
- room name = watch:tm_2
- join on watch:join
- seek sends position 2 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:tm_2");
socket.to("watch:tm_2").emit("playback", {
  position: 2,
  serverTime: Date.now(),
});
```

- homework
```ts
// 11a  room for tm_2
// 11b  fields position and serverTime
// 11c  where the JWT is read
// 11d  remove the listener when the screen for tm_2 closes
```

- example 12. socket room for up_6
- room name = watch:up_6
- join on watch:join
- seek sends position 200 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:up_6");
socket.to("watch:up_6").emit("playback", {
  position: 200,
  serverTime: Date.now(),
});
```

- homework
```ts
// 12a  room for up_6
// 12b  fields position and serverTime
// 12c  where the JWT is read
// 12d  remove the listener when the screen for up_6 closes
```

- example 13. socket room for st_1
- room name = watch:st_1
- join on watch:join
- seek sends position 1500 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:st_1");
socket.to("watch:st_1").emit("playback", {
  position: 1500,
  serverTime: Date.now(),
});
```

- homework
```ts
// 13a  room for st_1
// 13b  fields position and serverTime
// 13c  where the JWT is read
// 13d  remove the listener when the screen for st_1 closes
```

- example 14. socket room for n_2
- room name = watch:n_2
- join on watch:join
- seek sends position 24 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:n_2");
socket.to("watch:n_2").emit("playback", {
  position: 24,
  serverTime: Date.now(),
});
```

- homework
```ts
// 14a  room for n_2
// 14b  fields position and serverTime
// 14c  where the JWT is read
// 14d  remove the listener when the screen for n_2 closes
```

- example 15. socket room for b_3
- room name = watch:b_3
- join on watch:join
- seek sends position 4 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:b_3");
socket.to("watch:b_3").emit("playback", {
  position: 4,
  serverTime: Date.now(),
});
```

- homework
```ts
// 15a  room for b_3
// 15b  fields position and serverTime
// 15c  where the JWT is read
// 15d  remove the listener when the screen for b_3 closes
```

- example 16. socket room for se_1
- room name = watch:se_1
- join on watch:join
- seek sends position 15 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:se_1");
socket.to("watch:se_1").emit("playback", {
  position: 15,
  serverTime: Date.now(),
});
```

- homework
```ts
// 16a  room for se_1
// 16b  fields position and serverTime
// 16c  where the JWT is read
// 16d  remove the listener when the screen for se_1 closes
```

- example 17. socket room for q_1
- room name = watch:q_1
- join on watch:join
- seek sends position 10 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:q_1");
socket.to("watch:q_1").emit("playback", {
  position: 10,
  serverTime: Date.now(),
});
```

- homework
```ts
// 17a  room for q_1
// 17b  fields position and serverTime
// 17c  where the JWT is read
// 17d  remove the listener when the screen for q_1 closes
```

- example 18. socket room for r_1
- room name = watch:r_1
- join on watch:join
- seek sends position 2 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:r_1");
socket.to("watch:r_1").emit("playback", {
  position: 2,
  serverTime: Date.now(),
});
```

- homework
```ts
// 18a  room for r_1
// 18b  fields position and serverTime
// 18c  where the JWT is read
// 18d  remove the listener when the screen for r_1 closes
```

- example 19. socket room for pc_12
- room name = watch:pc_12
- join on watch:join
- seek sends position 72 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:pc_12");
socket.to("watch:pc_12").emit("playback", {
  position: 72,
  serverTime: Date.now(),
});
```

- homework
```ts
// 19a  room for pc_12
// 19b  fields position and serverTime
// 19c  where the JWT is read
// 19d  remove the listener when the screen for pc_12 closes
```

- example 20. socket room for m_1
- room name = watch:m_1
- join on watch:join
- seek sends position 3 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:m_1");
socket.to("watch:m_1").emit("playback", {
  position: 3,
  serverTime: Date.now(),
});
```

- homework
```ts
// 20a  room for m_1
// 20b  fields position and serverTime
// 20c  where the JWT is read
// 20d  remove the listener when the screen for m_1 closes
```

- example 21. socket room for t_9
- room name = watch:t_9
- join on watch:join
- seek sends position 16 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:t_9");
socket.to("watch:t_9").emit("playback", {
  position: 16,
  serverTime: Date.now(),
});
```

- homework
```ts
// 21a  room for t_9
// 21b  fields position and serverTime
// 21c  where the JWT is read
// 21d  remove the listener when the screen for t_9 closes
```

- example 22. socket room for v_4
- room name = watch:v_4
- join on watch:join
- seek sends position 6 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:v_4");
socket.to("watch:v_4").emit("playback", {
  position: 6,
  serverTime: Date.now(),
});
```

- homework
```ts
// 22a  room for v_4
// 22b  fields position and serverTime
// 22c  where the JWT is read
// 22d  remove the listener when the screen for v_4 closes
```

- example 23. socket room for f_2
- room name = watch:f_2
- join on watch:join
- seek sends position 1 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:f_2");
socket.to("watch:f_2").emit("playback", {
  position: 1,
  serverTime: Date.now(),
});
```

- homework
```ts
// 23a  room for f_2
// 23b  fields position and serverTime
// 23c  where the JWT is read
// 23d  remove the listener when the screen for f_2 closes
```

- example 24. socket room for u_3
- room name = watch:u_3
- join on watch:join
- seek sends position 1 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:u_3");
socket.to("watch:u_3").emit("playback", {
  position: 1,
  serverTime: Date.now(),
});
```

- homework
```ts
// 24a  room for u_3
// 24b  fields position and serverTime
// 24c  where the JWT is read
// 24d  remove the listener when the screen for u_3 closes
```

- example 25. socket room for pay_8
- room name = watch:pay_8
- join on watch:join
- seek sends position 15000 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:pay_8");
socket.to("watch:pay_8").emit("playback", {
  position: 15000,
  serverTime: Date.now(),
});
```

- homework
```ts
// 25a  room for pay_8
// 25b  fields position and serverTime
// 25c  where the JWT is read
// 25d  remove the listener when the screen for pay_8 closes
```

- example 26. socket room for room_2
- room name = watch:room_2
- join on watch:join
- seek sends position 12 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:room_2");
socket.to("watch:room_2").emit("playback", {
  position: 12,
  serverTime: Date.now(),
});
```

- homework
```ts
// 26a  room for room_2
// 26b  fields position and serverTime
// 26c  where the JWT is read
// 26d  remove the listener when the screen for room_2 closes
```

- example 27. socket room for vs_7
- room name = watch:vs_7
- join on watch:join
- seek sends position 48 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:vs_7");
socket.to("watch:vs_7").emit("playback", {
  position: 48,
  serverTime: Date.now(),
});
```

- homework
```ts
// 27a  room for vs_7
// 27b  fields position and serverTime
// 27c  where the JWT is read
// 27d  remove the listener when the screen for vs_7 closes
```

- example 28. socket room for sc_3
- room name = watch:sc_3
- join on watch:join
- seek sends position 11 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:sc_3");
socket.to("watch:sc_3").emit("playback", {
  position: 11,
  serverTime: Date.now(),
});
```

- homework
```ts
// 28a  room for sc_3
// 28b  fields position and serverTime
// 28c  where the JWT is read
// 28d  remove the listener when the screen for sc_3 closes
```

- example 29. socket room for tm_2
- room name = watch:tm_2
- join on watch:join
- seek sends position 2 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:tm_2");
socket.to("watch:tm_2").emit("playback", {
  position: 2,
  serverTime: Date.now(),
});
```

- homework
```ts
// 29a  room for tm_2
// 29b  fields position and serverTime
// 29c  where the JWT is read
// 29d  remove the listener when the screen for tm_2 closes
```

- example 30. socket room for up_6
- room name = watch:up_6
- join on watch:join
- seek sends position 200 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:up_6");
socket.to("watch:up_6").emit("playback", {
  position: 200,
  serverTime: Date.now(),
});
```

- homework
```ts
// 30a  room for up_6
// 30b  fields position and serverTime
// 30c  where the JWT is read
// 30d  remove the listener when the screen for up_6 closes
```

- example 31. socket room for st_1
- room name = watch:st_1
- join on watch:join
- seek sends position 1500 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:st_1");
socket.to("watch:st_1").emit("playback", {
  position: 1500,
  serverTime: Date.now(),
});
```

- homework
```ts
// 31a  room for st_1
// 31b  fields position and serverTime
// 31c  where the JWT is read
// 31d  remove the listener when the screen for st_1 closes
```

- example 32. socket room for n_2
- room name = watch:n_2
- join on watch:join
- seek sends position 24 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:n_2");
socket.to("watch:n_2").emit("playback", {
  position: 24,
  serverTime: Date.now(),
});
```

- homework
```ts
// 32a  room for n_2
// 32b  fields position and serverTime
// 32c  where the JWT is read
// 32d  remove the listener when the screen for n_2 closes
```

- example 33. socket room for b_3
- room name = watch:b_3
- join on watch:join
- seek sends position 4 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:b_3");
socket.to("watch:b_3").emit("playback", {
  position: 4,
  serverTime: Date.now(),
});
```

- homework
```ts
// 33a  room for b_3
// 33b  fields position and serverTime
// 33c  where the JWT is read
// 33d  remove the listener when the screen for b_3 closes
```

- example 34. socket room for se_1
- room name = watch:se_1
- join on watch:join
- seek sends position 15 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:se_1");
socket.to("watch:se_1").emit("playback", {
  position: 15,
  serverTime: Date.now(),
});
```

- homework
```ts
// 34a  room for se_1
// 34b  fields position and serverTime
// 34c  where the JWT is read
// 34d  remove the listener when the screen for se_1 closes
```

- example 35. socket room for q_1
- room name = watch:q_1
- join on watch:join
- seek sends position 10 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:q_1");
socket.to("watch:q_1").emit("playback", {
  position: 10,
  serverTime: Date.now(),
});
```

- homework
```ts
// 35a  room for q_1
// 35b  fields position and serverTime
// 35c  where the JWT is read
// 35d  remove the listener when the screen for q_1 closes
```

- example 36. socket room for r_1
- room name = watch:r_1
- join on watch:join
- seek sends position 2 and the server time
- token is checked on the handshake, not from a user id in the body
- socket.to sends to the others. io.to includes the sender

```ts
socket.join("watch:r_1");
socket.to("watch:r_1").emit("playback", {
  position: 2,
  serverTime: Date.now(),
});
```

- homework
```ts
// 36a  room for r_1
// 36b  fields position and serverTime
// 36c  where the JWT is read
// 36d  remove the listener when the screen for r_1 closes
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

- example 1. webhook id pc_12 and an upload named Ocean Star
- verify the signature on the raw body
- store pc_12 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 84 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "pc_12" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 84 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 1a  verify before JSON.parse
// 1b  unique id pc_12
// 1c  playlist extension m3u8
// 1d  captions are WebVTT
```

- example 2. webhook id m_1 and an upload named Ada
- verify the signature on the raw body
- store m_1 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 1 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "m_1" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 2 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 2a  verify before JSON.parse
// 2b  unique id m_1
// 2c  playlist extension m3u8
// 2d  captions are WebVTT
```

- example 3. webhook id t_9 and an upload named Rajshahi Open
- verify the signature on the raw body
- store t_9 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 8 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "t_9" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 8 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 3a  verify before JSON.parse
// 3b  unique id t_9
// 3c  playlist extension m3u8
// 3d  captions are WebVTT
```

- example 4. webhook id v_4 and an upload named Episode 1
- verify the signature on the raw body
- store v_4 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 10 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "v_4" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 10 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 4a  verify before JSON.parse
// 4b  unique id v_4
// 4c  playlist extension m3u8
// 4d  captions are WebVTT
```

- example 5. webhook id f_2 and an upload named NOR
- verify the signature on the raw body
- store f_2 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 2 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "f_2" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 2 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 5a  verify before JSON.parse
// 5b  unique id f_2
// 5c  playlist extension m3u8
// 5d  captions are WebVTT
```

- example 6. webhook id u_3 and an upload named a@example.com
- verify the signature on the raw body
- store u_3 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 1 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "u_3" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 2 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 6a  verify before JSON.parse
// 6b  unique id u_3
// 6c  playlist extension m3u8
// 6d  captions are WebVTT
```

- example 7. webhook id pay_8 and an upload named evt_1
- verify the signature on the raw body
- store pay_8 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 7500 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "pay_8" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 7500 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 7a  verify before JSON.parse
// 7b  unique id pay_8
// 7c  playlist extension m3u8
// 7d  captions are WebVTT
```

- example 8. webhook id room_2 and an upload named s_9
- verify the signature on the raw body
- store room_2 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 40 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "room_2" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 40 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 8a  verify before JSON.parse
// 8b  unique id room_2
// 8c  playlist extension m3u8
// 8d  captions are WebVTT
```

- example 9. webhook id vs_7 and an upload named North Wind
- verify the signature on the raw body
- store vs_7 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 60 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "vs_7" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 60 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 9a  verify before JSON.parse
// 9b  unique id vs_7
// 9c  playlist extension m3u8
// 9d  captions are WebVTT
```

- example 10. webhook id sc_3 and an upload named 11
- verify the signature on the raw body
- store sc_3 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 7 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "sc_3" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 7 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 10a  verify before JSON.parse
// 10b  unique id sc_3
// 10c  playlist extension m3u8
// 10d  captions are WebVTT
```

- example 11. webhook id tm_2 and an upload named Blue
- verify the signature on the raw body
- store tm_2 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 1 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "tm_2" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 2 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 11a  verify before JSON.parse
// 11b  unique id tm_2
// 11c  playlist extension m3u8
// 11d  captions are WebVTT
```

- example 12. webhook id up_6 and an upload named videos/u_1/a.mp4
- verify the signature on the raw body
- store up_6 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 6 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "up_6" } });
```

```bash
ffmpeg -i videos/u_1/a.mp4 -codec copy -start_number 0 -hls_time 6 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 12a  verify before JSON.parse
// 12b  unique id up_6
// 12c  playlist extension m3u8
// 12d  captions are WebVTT
```

- example 13. webhook id st_1 and an upload named player_a
- verify the signature on the raw body
- store st_1 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 1700 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "st_1" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 1700 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 13a  verify before JSON.parse
// 13b  unique id st_1
// 13c  playlist extension m3u8
// 13d  captions are WebVTT
```

- example 14. webhook id n_2 and an upload named Chattogram
- verify the signature on the raw body
- store n_2 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 30 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "n_2" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 30 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 14a  verify before JSON.parse
// 14b  unique id n_2
// 14c  playlist extension m3u8
// 14d  captions are WebVTT
```

- example 15. webhook id b_3 and an upload named semi
- verify the signature on the raw body
- store b_3 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 2 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "b_3" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 2 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 15a  verify before JSON.parse
// 15b  unique id b_3
// 15c  playlist extension m3u8
// 15d  captions are WebVTT
```

- example 16. webhook id se_1 and an upload named u_3
- verify the signature on the raw body
- store se_1 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 60 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "se_1" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 60 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 16a  verify before JSON.parse
// 16b  unique id se_1
// 16c  playlist extension m3u8
// 16d  captions are WebVTT
```

- example 17. webhook id q_1 and an upload named laytime
- verify the signature on the raw body
- store q_1 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 3 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "q_1" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 3 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 17a  verify before JSON.parse
// 17b  unique id q_1
// 17c  playlist extension m3u8
// 17d  captions are WebVTT
```

- example 18. webhook id r_1 and an upload named operator
- verify the signature on the raw body
- store r_1 in a unique column
- the second delivery hits the unique rule and does not insert again
- object storage holds the bytes. the database holds the key
- ffmpeg writes a playlist. segments are 4 seconds in this example

```ts
const event = stripe.webhooks.constructEvent(rawBody, signature, secret);
await db.stripeEvent.create({ data: { id: "r_1" } });
```

```bash
ffmpeg -i upload.mp4 -codec copy -start_number 0 -hls_time 4 -hls_list_size 0 -f hls stream.m3u8
```

- homework
```ts
// 18a  verify before JSON.parse
// 18b  unique id r_1
// 18c  playlist extension m3u8
// 18d  captions are WebVTT
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

- example 1. page and validate port-calls
- cursor after pc_12, limit 72
- offset page is fine for a numbered table
- Zod requires vesselName at least 1 character
- optional new fields are usually safe. renaming vesselName breaks old clients
- /v1/port-calls stays until the phone app moves

```ts
const Body = z.object({
  vesselName: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "pc_12" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 1a  cursor page after pc_12
// 1b  adding an optional note field is not a break
// 1c  renaming vesselName is a break
// 1d  Zod failure is 400. a duplicate id is 409
```

- example 2. page and validate matches
- cursor after m_1, limit 3
- offset page is fine for a numbered table
- Zod requires home at least 1 character
- optional new fields are usually safe. renaming home breaks old clients
- /v1/matches stays until the phone app moves

```ts
const Body = z.object({
  home: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "m_1" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 2a  cursor page after m_1
// 2b  adding an optional note field is not a break
// 2c  renaming home is a break
// 2d  Zod failure is 400. a duplicate id is 409
```

- example 3. page and validate tournaments
- cursor after t_9, limit 16
- offset page is fine for a numbered table
- Zod requires name at least 1 character
- optional new fields are usually safe. renaming name breaks old clients
- /v1/tournaments stays until the phone app moves

```ts
const Body = z.object({
  name: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "t_9" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 3a  cursor page after t_9
// 3b  adding an optional note field is not a break
// 3c  renaming name is a break
// 3d  Zod failure is 400. a duplicate id is 409
```

- example 4. page and validate videos
- cursor after v_4, limit 6
- offset page is fine for a numbered table
- Zod requires title at least 1 character
- optional new fields are usually safe. renaming title breaks old clients
- /v1/videos stays until the phone app moves

```ts
const Body = z.object({
  title: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "v_4" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 4a  cursor page after v_4
// 4b  adding an optional note field is not a break
// 4c  renaming title is a break
// 4d  Zod failure is 400. a duplicate id is 409
```

- example 5. page and validate facts
- cursor after f_2, limit 1
- offset page is fine for a numbered table
- Zod requires kind at least 1 character
- optional new fields are usually safe. renaming kind breaks old clients
- /v1/facts stays until the phone app moves

```ts
const Body = z.object({
  kind: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "f_2" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 5a  cursor page after f_2
// 5b  adding an optional note field is not a break
// 5c  renaming kind is a break
// 5d  Zod failure is 400. a duplicate id is 409
```

- example 6. page and validate users
- cursor after u_3, limit 1
- offset page is fine for a numbered table
- Zod requires email at least 1 character
- optional new fields are usually safe. renaming email breaks old clients
- /v1/users stays until the phone app moves

```ts
const Body = z.object({
  email: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "u_3" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 6a  cursor page after u_3
// 6b  adding an optional note field is not a break
// 6c  renaming email is a break
// 6d  Zod failure is 400. a duplicate id is 409
```

- example 7. page and validate payments
- cursor after pay_8, limit 20
- offset page is fine for a numbered table
- Zod requires eventId at least 1 character
- optional new fields are usually safe. renaming eventId breaks old clients
- /v1/payments stays until the phone app moves

```ts
const Body = z.object({
  eventId: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "pay_8" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 7a  cursor page after pay_8
// 7b  adding an optional note field is not a break
// 7c  renaming eventId is a break
// 7d  Zod failure is 400. a duplicate id is 409
```

- example 8. page and validate rooms
- cursor after room_2, limit 12
- offset page is fine for a numbered table
- Zod requires sessionId at least 1 character
- optional new fields are usually safe. renaming sessionId breaks old clients
- /v1/rooms stays until the phone app moves

```ts
const Body = z.object({
  sessionId: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "room_2" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 8a  cursor page after room_2
// 8b  adding an optional note field is not a break
// 8c  renaming sessionId is a break
// 8d  Zod failure is 400. a duplicate id is 409
```

- example 9. page and validate vessels
- cursor after vs_7, limit 48
- offset page is fine for a numbered table
- Zod requires name at least 1 character
- optional new fields are usually safe. renaming name breaks old clients
- /v1/vessels stays until the phone app moves

```ts
const Body = z.object({
  name: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "vs_7" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 9a  cursor page after vs_7
// 9b  adding an optional note field is not a break
// 9c  renaming name is a break
// 9d  Zod failure is 400. a duplicate id is 409
```

- example 10. page and validate scores
- cursor after sc_3, limit 11
- offset page is fine for a numbered table
- Zod requires points at least 1 character
- optional new fields are usually safe. renaming points breaks old clients
- /v1/scores stays until the phone app moves

```ts
const Body = z.object({
  points: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "sc_3" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 10a  cursor page after sc_3
// 10b  adding an optional note field is not a break
// 10c  renaming points is a break
// 10d  Zod failure is 400. a duplicate id is 409
```

- example 11. page and validate teams
- cursor after tm_2, limit 2
- offset page is fine for a numbered table
- Zod requires name at least 1 character
- optional new fields are usually safe. renaming name breaks old clients
- /v1/teams stays until the phone app moves

```ts
const Body = z.object({
  name: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "tm_2" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 11a  cursor page after tm_2
// 11b  adding an optional note field is not a break
// 11c  renaming name is a break
// 11d  Zod failure is 400. a duplicate id is 409
```

- example 12. page and validate uploads
- cursor after up_6, limit 20
- offset page is fine for a numbered table
- Zod requires key at least 1 character
- optional new fields are usually safe. renaming key breaks old clients
- /v1/uploads stays until the phone app moves

```ts
const Body = z.object({
  key: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "up_6" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 12a  cursor page after up_6
// 12b  adding an optional note field is not a break
// 12c  renaming key is a break
// 12d  Zod failure is 400. a duplicate id is 409
```

- example 13. page and validate standings
- cursor after st_1, limit 20
- offset page is fine for a numbered table
- Zod requires player at least 1 character
- optional new fields are usually safe. renaming player breaks old clients
- /v1/standings stays until the phone app moves

```ts
const Body = z.object({
  player: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "st_1" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 13a  cursor page after st_1
// 13b  adding an optional note field is not a break
// 13c  renaming player is a break
// 13d  Zod failure is 400. a duplicate id is 409
```

- example 14. page and validate notices
- cursor after n_2, limit 24
- offset page is fine for a numbered table
- Zod requires port at least 1 character
- optional new fields are usually safe. renaming port breaks old clients
- /v1/notices stays until the phone app moves

```ts
const Body = z.object({
  port: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "n_2" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 14a  cursor page after n_2
// 14b  adding an optional note field is not a break
// 14c  renaming port is a break
// 14d  Zod failure is 400. a duplicate id is 409
```

- example 15. page and validate brackets
- cursor after b_3, limit 4
- offset page is fine for a numbered table
- Zod requires round at least 1 character
- optional new fields are usually safe. renaming round breaks old clients
- /v1/brackets stays until the phone app moves

```ts
const Body = z.object({
  round: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "b_3" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 15a  cursor page after b_3
// 15b  adding an optional note field is not a break
// 15c  renaming round is a break
// 15d  Zod failure is 400. a duplicate id is 409
```

- example 16. page and validate sessions
- cursor after se_1, limit 15
- offset page is fine for a numbered table
- Zod requires userId at least 1 character
- optional new fields are usually safe. renaming userId breaks old clients
- /v1/sessions stays until the phone app moves

```ts
const Body = z.object({
  userId: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "se_1" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 16a  cursor page after se_1
// 16b  adding an optional note field is not a break
// 16c  renaming userId is a break
// 16d  Zod failure is 400. a duplicate id is 409
```

- example 17. page and validate queues
- cursor after q_1, limit 10
- offset page is fine for a numbered table
- Zod requires name at least 1 character
- optional new fields are usually safe. renaming name breaks old clients
- /v1/queues stays until the phone app moves

```ts
const Body = z.object({
  name: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "q_1" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 17a  cursor page after q_1
// 17b  adding an optional note field is not a break
// 17c  renaming name is a break
// 17d  Zod failure is 400. a duplicate id is 409
```

- example 18. page and validate roles
- cursor after r_1, limit 2
- offset page is fine for a numbered table
- Zod requires name at least 1 character
- optional new fields are usually safe. renaming name breaks old clients
- /v1/roles stays until the phone app moves

```ts
const Body = z.object({
  name: z.string().min(1).max(120),
});
const rows = await db.item.findMany({
  take: 20,
  skip: 1,
  cursor: { id: "r_1" },
  orderBy: { id: "asc" },
});
```

- homework
```ts
// 18a  cursor page after r_1
// 18b  adding an optional note field is not a break
// 18c  renaming name is a break
// 18d  Zod failure is 400. a duplicate id is 409
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

- example 1. a test and a rating beside port-calls
- laytime(72, 84, 15000) => demurrage 7500.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id pc_12. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("port-calls pc_12", () => {
  const money = laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 });
  assert.equal(money.demurrage, 7500.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 1a  demurrage 7500.0 for allowed 72 used 84 rate 15000
// 1b  two fields never logged
// 1c  Anemi runner is node:test
// 1d  Bracket runner is Vitest
```

- example 2. a test and a rating beside matches
- laytime(3, 1, 32) => demurrage 0, despatch 1.33
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id m_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("matches m_1", () => {
  const money = laytime({ allowedHours: 3, usedHours: 1, demurragePerDay: 32 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 2a  demurrage 0 for allowed 3 used 1 rate 32
// 2b  two fields never logged
// 2c  Anemi runner is node:test
// 2d  Bracket runner is Vitest
```

- example 3. a test and a rating beside tournaments
- laytime(16, 8, 24) => demurrage 0, despatch 4.0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id t_9. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("tournaments t_9", () => {
  const money = laytime({ allowedHours: 16, usedHours: 8, demurragePerDay: 24 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 3a  demurrage 0 for allowed 16 used 8 rate 24
// 3b  two fields never logged
// 3c  Anemi runner is node:test
// 3d  Bracket runner is Vitest
```

- example 4. a test and a rating beside videos
- laytime(6, 10, 6) => demurrage 1.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id v_4. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("videos v_4", () => {
  const money = laytime({ allowedHours: 6, usedHours: 10, demurragePerDay: 6 });
  assert.equal(money.demurrage, 1.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 4a  demurrage 1.0 for allowed 6 used 10 rate 6
// 4b  two fields never logged
// 4c  Anemi runner is node:test
// 4d  Bracket runner is Vitest
```

- example 5. a test and a rating beside facts
- laytime(1, 2, 500) => demurrage 20.83, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id f_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("facts f_2", () => {
  const money = laytime({ allowedHours: 1, usedHours: 2, demurragePerDay: 500 });
  assert.equal(money.demurrage, 20.83);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 5a  demurrage 20.83 for allowed 1 used 2 rate 500
// 5b  two fields never logged
// 5c  Anemi runner is node:test
// 5d  Bracket runner is Vitest
```

- example 6. a test and a rating beside users
- laytime(1, 1, 12) => demurrage 0, despatch 0.0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id u_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("users u_3", () => {
  const money = laytime({ allowedHours: 1, usedHours: 1, demurragePerDay: 12 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 6a  demurrage 0 for allowed 1 used 1 rate 12
// 6b  two fields never logged
// 6c  Anemi runner is node:test
// 6d  Bracket runner is Vitest
```

- example 7. a test and a rating beside payments
- laytime(15000, 7500, 1) => demurrage 0, despatch 156.25
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id pay_8. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("payments pay_8", () => {
  const money = laytime({ allowedHours: 15000, usedHours: 7500, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 7a  demurrage 0 for allowed 15000 used 7500 rate 1
// 7b  two fields never logged
// 7c  Anemi runner is node:test
// 7d  Bracket runner is Vitest
```

- example 8. a test and a rating beside rooms
- laytime(12, 40, 1) => demurrage 1.17, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id room_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("rooms room_2", () => {
  const money = laytime({ allowedHours: 12, usedHours: 40, demurragePerDay: 1 });
  assert.equal(money.demurrage, 1.17);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 8a  demurrage 1.17 for allowed 12 used 40 rate 1
// 8b  two fields never logged
// 8c  Anemi runner is node:test
// 8d  Bracket runner is Vitest
```

- example 9. a test and a rating beside vessels
- laytime(48, 60, 8000) => demurrage 4000.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id vs_7. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("vessels vs_7", () => {
  const money = laytime({ allowedHours: 48, usedHours: 60, demurragePerDay: 8000 });
  assert.equal(money.demurrage, 4000.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 9a  demurrage 4000.0 for allowed 48 used 60 rate 8000
// 9b  two fields never logged
// 9c  Anemi runner is node:test
// 9d  Bracket runner is Vitest
```

- example 10. a test and a rating beside scores
- laytime(11, 7, 32) => demurrage 0, despatch 2.67
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id sc_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("scores sc_3", () => {
  const money = laytime({ allowedHours: 11, usedHours: 7, demurragePerDay: 32 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 10a  demurrage 0 for allowed 11 used 7 rate 32
// 10b  two fields never logged
// 10c  Anemi runner is node:test
// 10d  Bracket runner is Vitest
```

- example 11. a test and a rating beside teams
- laytime(2, 1, 10) => demurrage 0, despatch 0.21
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id tm_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("teams tm_2", () => {
  const money = laytime({ allowedHours: 2, usedHours: 1, demurragePerDay: 10 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 11a  demurrage 0 for allowed 2 used 1 rate 10
// 11b  two fields never logged
// 11c  Anemi runner is node:test
// 11d  Bracket runner is Vitest
```

- example 12. a test and a rating beside uploads
- laytime(200, 6, 1) => demurrage 0, despatch 4.04
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id up_6. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("uploads up_6", () => {
  const money = laytime({ allowedHours: 200, usedHours: 6, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 12a  demurrage 0 for allowed 200 used 6 rate 1
// 12b  two fields never logged
// 12c  Anemi runner is node:test
// 12d  Bracket runner is Vitest
```

- example 13. a test and a rating beside standings
- laytime(1500, 1700, 32) => demurrage 266.67, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id st_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("standings st_1", () => {
  const money = laytime({ allowedHours: 1500, usedHours: 1700, demurragePerDay: 32 });
  assert.equal(money.demurrage, 266.67);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 13a  demurrage 266.67 for allowed 1500 used 1700 rate 32
// 13b  two fields never logged
// 13c  Anemi runner is node:test
// 13d  Bracket runner is Vitest
```

- example 14. a test and a rating beside notices
- laytime(24, 30, 12000) => demurrage 3000.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id n_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("notices n_2", () => {
  const money = laytime({ allowedHours: 24, usedHours: 30, demurragePerDay: 12000 });
  assert.equal(money.demurrage, 3000.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 14a  demurrage 3000.0 for allowed 24 used 30 rate 12000
// 14b  two fields never logged
// 14c  Anemi runner is node:test
// 14d  Bracket runner is Vitest
```

- example 15. a test and a rating beside brackets
- laytime(4, 2, 16) => demurrage 0, despatch 0.67
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id b_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("brackets b_3", () => {
  const money = laytime({ allowedHours: 4, usedHours: 2, demurragePerDay: 16 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 15a  demurrage 0 for allowed 4 used 2 rate 16
// 15b  two fields never logged
// 15c  Anemi runner is node:test
// 15d  Bracket runner is Vitest
```

- example 16. a test and a rating beside sessions
- laytime(15, 60, 1) => demurrage 1.88, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id se_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("sessions se_1", () => {
  const money = laytime({ allowedHours: 15, usedHours: 60, demurragePerDay: 1 });
  assert.equal(money.demurrage, 1.88);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 16a  demurrage 1.88 for allowed 15 used 60 rate 1
// 16b  two fields never logged
// 16c  Anemi runner is node:test
// 16d  Bracket runner is Vitest
```

- example 17. a test and a rating beside queues
- laytime(10, 3, 1) => demurrage 0, despatch 0.15
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id q_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("queues q_1", () => {
  const money = laytime({ allowedHours: 10, usedHours: 3, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 17a  demurrage 0 for allowed 10 used 3 rate 1
// 17b  two fields never logged
// 17c  Anemi runner is node:test
// 17d  Bracket runner is Vitest
```

- example 18. a test and a rating beside roles
- laytime(2, 4, 1) => demurrage 0.08, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id r_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("roles r_1", () => {
  const money = laytime({ allowedHours: 2, usedHours: 4, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0.08);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 18a  demurrage 0.08 for allowed 2 used 4 rate 1
// 18b  two fields never logged
// 18c  Anemi runner is node:test
// 18d  Bracket runner is Vitest
```

- example 19. a test and a rating beside port-calls
- laytime(72, 84, 15000) => demurrage 7500.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id pc_12. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("port-calls pc_12", () => {
  const money = laytime({ allowedHours: 72, usedHours: 84, demurragePerDay: 15000 });
  assert.equal(money.demurrage, 7500.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 19a  demurrage 7500.0 for allowed 72 used 84 rate 15000
// 19b  two fields never logged
// 19c  Anemi runner is node:test
// 19d  Bracket runner is Vitest
```

- example 20. a test and a rating beside matches
- laytime(3, 1, 32) => demurrage 0, despatch 1.33
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id m_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("matches m_1", () => {
  const money = laytime({ allowedHours: 3, usedHours: 1, demurragePerDay: 32 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 20a  demurrage 0 for allowed 3 used 1 rate 32
// 20b  two fields never logged
// 20c  Anemi runner is node:test
// 20d  Bracket runner is Vitest
```

- example 21. a test and a rating beside tournaments
- laytime(16, 8, 24) => demurrage 0, despatch 4.0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id t_9. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("tournaments t_9", () => {
  const money = laytime({ allowedHours: 16, usedHours: 8, demurragePerDay: 24 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 21a  demurrage 0 for allowed 16 used 8 rate 24
// 21b  two fields never logged
// 21c  Anemi runner is node:test
// 21d  Bracket runner is Vitest
```

- example 22. a test and a rating beside videos
- laytime(6, 10, 6) => demurrage 1.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id v_4. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("videos v_4", () => {
  const money = laytime({ allowedHours: 6, usedHours: 10, demurragePerDay: 6 });
  assert.equal(money.demurrage, 1.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 22a  demurrage 1.0 for allowed 6 used 10 rate 6
// 22b  two fields never logged
// 22c  Anemi runner is node:test
// 22d  Bracket runner is Vitest
```

- example 23. a test and a rating beside facts
- laytime(1, 2, 500) => demurrage 20.83, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id f_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("facts f_2", () => {
  const money = laytime({ allowedHours: 1, usedHours: 2, demurragePerDay: 500 });
  assert.equal(money.demurrage, 20.83);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 23a  demurrage 20.83 for allowed 1 used 2 rate 500
// 23b  two fields never logged
// 23c  Anemi runner is node:test
// 23d  Bracket runner is Vitest
```

- example 24. a test and a rating beside users
- laytime(1, 1, 12) => demurrage 0, despatch 0.0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id u_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("users u_3", () => {
  const money = laytime({ allowedHours: 1, usedHours: 1, demurragePerDay: 12 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 24a  demurrage 0 for allowed 1 used 1 rate 12
// 24b  two fields never logged
// 24c  Anemi runner is node:test
// 24d  Bracket runner is Vitest
```

- example 25. a test and a rating beside payments
- laytime(15000, 7500, 1) => demurrage 0, despatch 156.25
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id pay_8. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("payments pay_8", () => {
  const money = laytime({ allowedHours: 15000, usedHours: 7500, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 25a  demurrage 0 for allowed 15000 used 7500 rate 1
// 25b  two fields never logged
// 25c  Anemi runner is node:test
// 25d  Bracket runner is Vitest
```

- example 26. a test and a rating beside rooms
- laytime(12, 40, 1) => demurrage 1.17, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id room_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("rooms room_2", () => {
  const money = laytime({ allowedHours: 12, usedHours: 40, demurragePerDay: 1 });
  assert.equal(money.demurrage, 1.17);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 26a  demurrage 1.17 for allowed 12 used 40 rate 1
// 26b  two fields never logged
// 26c  Anemi runner is node:test
// 26d  Bracket runner is Vitest
```

- example 27. a test and a rating beside vessels
- laytime(48, 60, 8000) => demurrage 4000.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id vs_7. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("vessels vs_7", () => {
  const money = laytime({ allowedHours: 48, usedHours: 60, demurragePerDay: 8000 });
  assert.equal(money.demurrage, 4000.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 27a  demurrage 4000.0 for allowed 48 used 60 rate 8000
// 27b  two fields never logged
// 27c  Anemi runner is node:test
// 27d  Bracket runner is Vitest
```

- example 28. a test and a rating beside scores
- laytime(11, 7, 32) => demurrage 0, despatch 2.67
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id sc_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("scores sc_3", () => {
  const money = laytime({ allowedHours: 11, usedHours: 7, demurragePerDay: 32 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 28a  demurrage 0 for allowed 11 used 7 rate 32
// 28b  two fields never logged
// 28c  Anemi runner is node:test
// 28d  Bracket runner is Vitest
```

- example 29. a test and a rating beside teams
- laytime(2, 1, 10) => demurrage 0, despatch 0.21
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id tm_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("teams tm_2", () => {
  const money = laytime({ allowedHours: 2, usedHours: 1, demurragePerDay: 10 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 29a  demurrage 0 for allowed 2 used 1 rate 10
// 29b  two fields never logged
// 29c  Anemi runner is node:test
// 29d  Bracket runner is Vitest
```

- example 30. a test and a rating beside uploads
- laytime(200, 6, 1) => demurrage 0, despatch 4.04
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id up_6. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("uploads up_6", () => {
  const money = laytime({ allowedHours: 200, usedHours: 6, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 30a  demurrage 0 for allowed 200 used 6 rate 1
// 30b  two fields never logged
// 30c  Anemi runner is node:test
// 30d  Bracket runner is Vitest
```

- example 31. a test and a rating beside standings
- laytime(1500, 1700, 32) => demurrage 266.67, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id st_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("standings st_1", () => {
  const money = laytime({ allowedHours: 1500, usedHours: 1700, demurragePerDay: 32 });
  assert.equal(money.demurrage, 266.67);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 31a  demurrage 266.67 for allowed 1500 used 1700 rate 32
// 31b  two fields never logged
// 31c  Anemi runner is node:test
// 31d  Bracket runner is Vitest
```

- example 32. a test and a rating beside notices
- laytime(24, 30, 12000) => demurrage 3000.0, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id n_2. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("notices n_2", () => {
  const money = laytime({ allowedHours: 24, usedHours: 30, demurragePerDay: 12000 });
  assert.equal(money.demurrage, 3000.0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 32a  demurrage 3000.0 for allowed 24 used 30 rate 12000
// 32b  two fields never logged
// 32c  Anemi runner is node:test
// 32d  Bracket runner is Vitest
```

- example 33. a test and a rating beside brackets
- laytime(4, 2, 16) => demurrage 0, despatch 0.67
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id b_3. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("brackets b_3", () => {
  const money = laytime({ allowedHours: 4, usedHours: 2, demurragePerDay: 16 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 33a  demurrage 0 for allowed 4 used 2 rate 16
// 33b  two fields never logged
// 33c  Anemi runner is node:test
// 33d  Bracket runner is Vitest
```

- example 34. a test and a rating beside sessions
- laytime(15, 60, 1) => demurrage 1.88, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id se_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("sessions se_1", () => {
  const money = laytime({ allowedHours: 15, usedHours: 60, demurragePerDay: 1 });
  assert.equal(money.demurrage, 1.88);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 34a  demurrage 1.88 for allowed 15 used 60 rate 1
// 34b  two fields never logged
// 34c  Anemi runner is node:test
// 34d  Bracket runner is Vitest
```

- example 35. a test and a rating beside queues
- laytime(10, 3, 1) => demurrage 0, despatch 0.15
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id q_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("queues q_1", () => {
  const money = laytime({ allowedHours: 10, usedHours: 3, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 35a  demurrage 0 for allowed 10 used 3 rate 1
// 35b  two fields never logged
// 35c  Anemi runner is node:test
// 35d  Bracket runner is Vitest
```

- example 36. a test and a rating beside roles
- laytime(2, 4, 1) => demurrage 0.08, despatch 0
- elo 1500 vs 1700, K 32, a win => expected about 0.2403, next about 1524.31
- log the id r_1. do not log a password or a refresh token
- Vitest is Bracket. node:test is Anemi
- VMS has Redis and BullMQ. Anemi does not

```ts
test("roles r_1", () => {
  const money = laytime({ allowedHours: 2, usedHours: 4, demurragePerDay: 1 });
  assert.equal(money.demurrage, 0.08);
});
// nextRating(1500, 1700, 1) about 1524.31
```

- homework
```ts
// 36a  demurrage 0.08 for allowed 2 used 4 rate 1
// 36b  two fields never logged
// 36c  Anemi runner is node:test
// 36d  Bracket runner is Vitest
```
