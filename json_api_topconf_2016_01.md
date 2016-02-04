
# _**{json:api}**_<br><br><br>

### The ultimate
### anti-bikeshedding weapon

^
- tough time slot
- lunch, tired
- nodding off, exercise
- json-api anti-bikeshedding
- clarification: jsonapi spec

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
- 24 episodes
- guests
  * Jafar Husain
  * Steve Klabnik
  * Ashley Williams

---

# _**{json:api}**_

^
- Lets talk about JSON:API
- BTW: This is the logo
- its not valid JSON

---

# **_{"json":"api"}_**

^
- Doesn't reflect on quality of project

---

## Aside from _HTTP_ and _JSON_ what do most API**s** have in _common_**?**

^
- assuming HTTP/JSON
- If consumed signif # APIs
- over time you come to
  realize that its ...

---

## Not Much**!**

^
- codes, q params, rel-ships
- spending lots of time learning
- and writing custom clients
- Over 6-7 years my experience
- Lots API integrat, big names
- inconsistencies betw end-p
- allways 200 OK
- HAVE to LEARN ALL THIS

---

# <br><br><br><br><br><br><br><br><br><br>"Each time you want to interact with a new API you have to rewrite a brand new client that speaks its particular flavor of whatever thing it is that you are building"


```json
                                       Steve Klabnik
```

^
- Paraphrase language analogy
- Giving talk in English ...
- cannot change how 3rd party
- But apply to your own

---

## _I don't need a standard,<br>_ I have my own thing_**!**_

^
- examples lead up to bikeshedding
- you spend lots of mental energy on trivial details

---

# _**bikeshedding**_

^
- Parkinson's law of triviality
- JSON-API can settle disputes

---

### Several attempts to solve this exist

### <br><br><br>_**HAL, Siren, OData,**_ **JSON-API**

^
- Json-api is not the first
- there are other standards
- jsonapi is well suited for SPA
- extracted from ember-data by Yehuda Kats

---

# _Covered by json-api spec_

Content Negotiation
Document Structure
Fetching Data
Creating, Updating and Deleting Resources
Query Parameters
Errors
HTTP status codes

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

```http
GET /articles/1 HTTP/1.1
```
```
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
- simple example: resource obj
- data: doc primary data
- type
- id
- attributes

---

```http
GET /articles/1 HTTP/1.1
```
```
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Hello world",
      "body": "bla bla bla",
      "created": "2016-01-10T14:00:02+01:00"
    },
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
} } } } }
```

^
- relationships

---

```http
GET /articles/1?include=author HTTP/1.1
```
```
{
  "data": {
    "type": "articles",
    "id": "1",
    "attributes": {
      :
    },
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
        },
        "data": { "type": "people", "id": "9" },
      }
    }
  }
  "included": []
}
```

^
- data: Resource Identifier Obj

---

```http
GET /articles/1?include=author HTTP/1.1
```
```
{
  "data": {
    :
  },
  "included": [
    {
      "type": "people",
      "id": "9",
      "attributes": {
        "first_name": "Joe",
        "last_name": "Smith"
      }
    }
  ]
}
```

^
- Why is good - reduce data duplication
- ...
- Spec also includes
- Allowed chars fr names
- Meta information
- pagination
- filtering

---

# _Fetching Data - Sparse Field Sets_

```http
GET /articles?fields[articles]=title,created HTTP/1.1
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

```
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
- allows community to build tools

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

## Who is using json-api?

### <br><br><br>_**booking.com<br>hood.ie<br>patreon.com<br>ember-data**_

---

## _`jsonapi.org`_

### <br><br><br><br><br><br>_`discuss.jsonapi.org`_

^
- Project website
...
- specification
- extensions
- recommendations
- examples
- implementations
...
- discourse forum

---

## **Thank You!**<br><br>

### _`If you want to talk more, feel free to contact me`_<br><br>
### _`@hglattergotz`_
### _`henning@glatter-gotz.com`_

^


