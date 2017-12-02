# graphvueflow

> Nuxt Vue Dgraph Pilot Project

## Build Setup

``` bash
# install dependencies
$ npm install # Or yarn install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm start

# generate static project
$ npm run generate
```

For detailed explanation on how things work, checkout the [Nuxt.js docs](https://github.com/nuxt/nuxt.js).

## Init Dgraph Database

clone graphoverflow using nuxt.js

curl https://get.dgraph.io -sSf | bash
dgraph zero --port_offset -2000
dgraph server --memory_mb 4096 --zero localhost:5080
http://localhost:8080 | dgraph browser

clone https://github.com/dgraph-io/graphoverflow.git
cd graphoverflow
for category in comments posts tags users votes; do go run $category/main.go -dir="/users/$USER/Downloads/codereview.stackexchange.com" -output="$category.rdf.gz"; done

# https://discuss.dgraph.io/t/major-changes-in-v0-9/1886
1. POST /query to do queries.
2. POST /mutate to do mutations. Mutation block doesn’t require the mutation keyword anymore since we have a separate endpoint for it.
3. POST /commit to commit a transaction.
4. PUT /abort to abort a transaction.
5. PUT /alter to drop a predicate, drop all data and do schema mutations.

curl -X PUT localhost:8080/alter -d $'
    AboutMe: string .
    Author: uid @reverse .
    Owner: uid @reverse .
    DisplayName: string .
    Location: string .
    Reputation: int .
    Score: int .
    Text: string @index(fulltext) .
    Tag.Text: string @index(hash) .
    Type: string @index(exact) .
    ViewCount: int @index(int) .
    Vote: uid @reverse .
    Title: uid @reverse .
    Body: uid @reverse .
    Post: uid @reverse .
    PostCount: int @index(int) .
    Tags: uid @reverse .
    Timestamp: datetime .
    GitHubID: string @index(hash) .
    Has.Answer: uid @reverse @count .
    Chosen.Answer: uid @count .
    Comment: uid @reverse .
    Upvote: uid @reverse .
    Downvote: uid @reverse .
    Tag: uid @reverse .
'
for category in tags comments posts users votes; do dgraph live -r $category.rdf.gz -z 127.0.0.1:5080; done
