# Codestache

Codestache is a revised specification of Mustache template syntax to fit source
code generation needs.

There are a couple of motivations behind creating this specification;

- In Mustache it is hard to focus on code, all you see is mustache `{{}}`
- A code template should be a valid code in the target language.
- Code generation uses meta-data, while a Mustache template uses data, and this
  makes a huge difference.

To give an example of how a Codestache template is readable and easy to
maintain, below is a comparison between Mustache and Codestache.

## Mustache is nice

`SCHEMA: schema.json`

```json
{
    "functions": [
        {
            "name": "sayHello",
            "arguments": [
                {
                    "name": "name",
                    "type": "String"
                },
                {
                    "name": "surname",
                    "type": "String",
                    "_last": true
                }
            ]
        },
        {
            "name": "sayGoodbye",
            "arguments": [
                {
                    "name": "name",
                    "type": "String",
                    "_last": true
                }
            ]
        }
    ]
}
```

`TEMPLATE: template.mustache`

```mustache
{{#functions}}
function {{name}}(
    {{#arguments}}
    {{name}}{{^_last}},{{/_last}}
    {{/arguments}}
) {
    console.log("{{name}} called"
        {{#arguments}}
        + " ({{name}}: " + {{name}} + ")"
        {{/arguments}}
    );
}
{{/functions}}
```

`OUTPUT: code.js`

```javascript
function sayHello(
    name,
    surname
) {
    console.log("sayHello called"
        + " (name: " + name + ")"
        + " (surname: " + surname + ")"
    );
}
function sayGoodbye(
    name
) {
    console.log("sayGoodbye called"
        + " (name: " + name + ")"
    );
}
```

## Codestache is nicer

`SCHEMA: schema.json`

```json
{
    "$concepts": {
        "$function+": {
            "$argument*": "$type"
        }
    },
    "sayHello": {
        "name": "string",
        "surname": "string"
    },
    "sayGoodbye": {
        "name": "string"
    }
}
```

*Schema written in [JSON Concepts][]*

`TEMPLATE: template.js`

```javascript
/* #function */
function $function$(/* # */$argument$) {
    console.log("$function$ called"
        + " ($argument$: " + $argument$ + ")" // #argument
    );
}
/* / */
```

*Template is a valid javascript file.*

`OUTPUT: code.js`

```javascript
function sayHello(name, surname) {
    console.log("sayHello called"
        + " (name: " + name + ")"
        + " (surname: " + surname + ")"
    );
}
function sayGoodbye(name) {
    console.log("sayGoodbye called"
        + " (name: " + name + ")"
    );
}
```

[JSON Concepts]: https://github.com/codingatwill/json-concepts

Don't get me wrong. Mustache is a great template syntax for other stuff, but
to generate code Codestache is better.
