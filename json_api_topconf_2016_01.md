
# _**{json:api}**_<br><br><br>

### The ultimate
### anti-bikeshedding weapon

---

![inline](henning_center.png)
# Henning Glatter-Götz
# _**`@hglattergotz`**_

^ 
- Backed developer
- substantial part of work is integrating with or developing apis
- dabbling in Ember.js - reason for finding JSONAPI

---

_Podcasting at ..._

![inline](reactive_round.png)

# http://reactive.audio

_Panel discussions about current tech news with a focus on JavaScript_

---

... and also podcasting at

![inline](descriptive_round.png)

# _http://descriptive.audio_

Programmer origin stories

^ 
- Long format interviews
- 24 episodes with guests like Jafar Husain, Steve Klabnik, Ashley Williams

---

# _**{json:api}**_

^
- Lets talk about JSON:API
- BTW: This is the logo - its not JSON
- Does not reflect quality of project

---

# **_{_"_json_"_:_"_api_"_}_**

---

## Aside from _HTTP_ and _JSON_ what to most API**s** have in _common_**?**

^
- If you consume lots of APIs over time you will notice 

---

## Not Much**!**

^
- This means that you are spending a lot of time learning and writing custom clients
- Over last 6 years that has definitely been my experience
- Lots API integrat, big names
- And building API's int/ext

---

# <br><br><br><br><br><br><br><br><br><br>"Each time you want to interact with a new API you have to rewrite a brand new client that speaks its particular flavor of whatever thing it is that you are building"


```json
                                       Steve Klabnik
```

^
- Paraphrase the language analogy
- Giving talk in English, that is not native language of most
- Of course you cannot change how 3rd party provider make APIs
- But you can at least build your own according to a standard

---

## _I don't need a standard,<br>_ I have my own thing_**!**_

^
- Talk about mini decisions and bike shedding

---

# _**bikeshedding**_

^
- So you will spend a lot of mental energy on this trivial details
- My argument is that something like JSON-API can settle disputes and probably has better solutions that you have
come up with.

---

### Several attempts to solve this exist

### <br><br><br>_**HAL, Siren, OData,**_ **JSON-API**

^
- Json-api is not the first to try to solve this
- there are other standards
- jsonapi is well suited for SPA

---

# _Covered by json-api spec_

Content Negotiation
Document Structure
Fetching Data
Creating, Updating and Deleting Resources
Query Parameters
Errors

---

## _Content Negotiation_<br><br><br>


## [fit] `Content-Type: application/vnd.api+json`

^
- Both server and client must send header
- vnd = vendor specific

---

## _Document Structure_

^
Overview of small part of structure spec to give a sense of it

---

# Articles Model / People model

```json
  id:      <string>             id:         <string>
  title:   <string>             first_name: <string>
  body:    <string>             last_name:  <string>
  created: <datetime>
```

---

# _Single Resource_

```http
GET /articles/1 HTTP/1.1
```
```json
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Hello world",
      "body": "bla bla bla",
      "created": "2016-01-10T14:00:02+01:00"
    }
  }
}
```

^
- simple example of a resource object
- data: documents primary data
- type
- id
- attributes

---

# _Single Resource with_ Relationship

```http
GET /articles/1 HTTP/1.1
```
```json
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {"title": "...", "body": "...", "created": "..."},
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
    }}}}
}
```

^
- relationships
- data is a Resource identifier Object - is an object that identifies a resource
- ...
- Doc Struct also contains detailed specs for 
- Meta information
- Links
- Member names (allowed characters, etc)

---

# _Single Resource with_ included _Relationship_

```http
GET /articles/1?include=author HTTP/1.1
```
```json
{
  "data": {
    "type": "articles", "id": "1", "attributes": {},
    "relationships": {
      "author": {
        "links": {"self": "...", "related": "..."},
        "data": { "type": "people", "id": "9" }
      }
    }
  },
  "included": [{"type": "people", "id": "9", "attributes": {}}]
}
```

^
- relationships
- data is a Resource identifier Object - is an object that identifies a resource
- ...
- Doc Struct also contains detailed specs for 
- Meta information
- Links
- Member names (allowed characters, etc)

---

# _Fetching Data - Sparse Field Sets_

```http
GET /articles?fields[articles]=title,body HTTP/1.1
Accept: application/vnd.api+json
```

^
- Example of sparse fields

---

# _Fetching Data - Sorting_

```
ascending
```

```http
GET /articles?sort=created HTTP/1.1
```

```

descending
```

```http
GET /articles?sort=-created,title HTTP/1.1
```

^ - Specifies the parameter name SORT
- ALSO specification for PAGINATION and FILTERING

---

# _Errors_

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/vnd.api+json
```

```json
{
  "errors": [
    {
      "status": "422",
      "source": { "pointer": "/data/attributes/first-name" },
      "title":  "Invalid Attribute",
      "detail": "First name must contain at least three characters."
    }
  ]
}
```

^
Only requirement: Error objects must be returned as array keyed by ERRORS in top level

---

## So how does any of this help us?

---

# _**tooling**_

^
- Ensures repeatable results
- allows us to focus on acquiring the domain knowledge
- no longer need to write custom client code
- This is what I mean by bringing sanity

---

## Client Libraries

### <br><br><br>_**JavaScript, iOS, Ruby, PHP, Perl**_

^
YES, Perl (courtesy of Booking.com)

---

## Server Libraries

### <br><br><br>_**PHP, Node.js, Ruby, Python,<br>Go, .NET, Java, Elixir, Perl**_

^ YES, Perl

---

## Example<br><br><br><br>

# [fit] _Backend:_ neomerx/json-api _**(PHP)**_

# [fit] _Frontend:_ ember-data _**(JavaScript)**_

---

# _neomerx/json-api_

```php
01 $encoder = Encoder::instance([
02     ArticleModel::class => ArticleSchema::class
03     AuthorModel::class => AuthorSchema::class
04     ],
05     new EncoderOptions($encodeOptions, 'http://example.com')
06 );
07 
08 return $encoder->encodeData($articles);
```

---

```php
01 class ArticleSchema extends SchemaProvider
02 {
03     protected $resourceType = 'articles';
04 
05     public function getId($article)
06     {
07         return $article->getId();
08     }
09 
10     public function getAttributes($article)
11     {
12         return [
13             'title'   => $article->getTitle(),
14             'body'    => $article->getBody(),
15             'created' => $article->getCreated()
16         ];
17     }
18
19     public function getRelationships($article, array $includeList = [])
20     {
21         return [
22             'author' => [self::DATA => $article->getAuthor()],
23         ];
24     }
25 }
```

---

# _ember-data - article.js_

```javascript
import DS from 'ember-data';

export default DS.Model.extend({
    title: DS.attr('string'),
    body: DS.attr('string'),
    created: DS.attr('date'),
    author: DS.belongsTo('person')
});
```

---

# _ember-data - person.js_

```javascript
import DS from 'ember-data';

export default DS.Model.extend({
    first_name: DS.attr('string'),
    last_name: DS.attr('string'),
    articles: DS.hasMany('article')
});
```

---

## **Thank You!**<br><br>

### _`If you want to talk more, feel free to contact me`_<br><br>
### _`@hglattergotz`_
### _`henning@glatter-gotz.com`_

^


