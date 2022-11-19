# textbrowser-data-schemas

Schemas describing the type of data documents consumed by textbrowser.

While this repository only contains some general purpose schemas, we will describe here the type of data documents which they expect to check.

## JSON formats

JSON data files, the individual JSON schema file against which the data
file can be validated (and enhanced in a standard manner with type
information), and our custom JSON metadata format file for indicating
information such as designating fields for browsing ranges, are covered
below.

### JSON Data

JSON data files to represent your data (e.g.,
[this file](https://bitbucket.org/brettz9/bahaiwritings/src/6b07fb41d11ed76570f7da03ffc9d11aa8ff0a5d/data/writings/peace.json?at=master&fileviewer=file-view-default))
expect 3 fields (per the simple
[schema](https://github.com/bahaidev/textbrowser-data-schemas/blob/main/schemas/table-container.jsonschema)
and its simple
[array-of-arrays subschema](https://github.com/bahaidev/textbrowser/blob/main/schemas/array-of-arrays.jsonschema)
to which they adhere):

1. A `schema` JSON reference pointer to your data's JSON schema.
    See [JSON Schema](#json-schema).
2. A `metadata` JSON reference pointer to your data's JSON metadata.
    See [JSON Metadata](#json-metadata).
3. A `data` property which is an an array of arrays containing your tabular
    text itself. This property must adhere to the specific JSON schema
    referenced in the `schema` property above for this file.

### JSON Schema

This document must itself be a valid
[JSON Schema](https://json-schema.org/specification.html)
and must, moreover, adhere to the
[table format schema](https://github.com/bahaidev/textbrowser-data-schemas/blob/main/schemas/table.jsonschema).

This simple document, besides including the text content, designates a more precise JSON schema to indicate precise column types (e.g.,
[this one](https://bitbucket.org/brettz9/bahaiwritings/src/6b07fb41d11ed76570f7da03ffc9d11aa8ff0a5d/data/writings/schemas/Bible.jsonschema?at=master&fileviewer=file-view-default)).

This schema is expected to designate an array of arrays, with the items
of the inner arrays being more flexible JSON schemas representing individual
fields of your works, but expecting a `type` and `title` in all cases.

It may, for integers, optionally also have `minimum` and `maximum` properties
which can be used to set numeric inputs (assuming the min or max is not
dependent on another value, as often will be paragraph numbers within
chapters).

For strings, an optional `"format": "html"` can be present, though raw HTML in
the so-designated fields will only be output in the results display as HTML
if the `TextBrowser` option `trustFormatHTML` is set to `true`. This
is for security reasons in case an untrusted schema is being deployed, but
one will probably normally wish to set this to `true` for trusted data.

Also for string type fields may one use `enum`, and its values are
currently used to determine field values (looking in the metadata
file under the localized `fieldvalue` object whose key is the column in which
the value occurs) which are used in place of the original content within
pull-down menus for selecting verse ranges and in the corresponding
column for the field of the results display. (The `fieldvalue-aliases`
property of the specific field key of the main `fields` property will
also be checked in that file for indicating optional aliases of the given
field value.)

### JSON Metadata

[Meta-data JSON](https://github.com/bahaidev/textbrowser/blob/main/general-schemas/metadata.jsonschema) (e.g.,
[this one](https://bitbucket.org/brettz9/bahaiwritings/src/6b07fb41d11ed76570f7da03ffc9d11aa8ff0a5d/data/writings/metadata/Bible.metadata?at=master&fileviewer=file-view-default))
is required so as for you to indicate for the app how the multilinear text
is to be browsed (e.g., which fields can be used as sequential
chapter/paragraph/verse numbers, how its columns should be translated, etc.).

This file has two main properties, `table` and `fields`.

`table` has two properties, `browse_fields` and `default_view`.

`browse_fields` is a name-set object or an array of string field names or
name-set objects. Name-set objects have an optional `name` string, a `set`
array of field name strings, and an optional `presort` boolean to
indicate whether the table must first sort the elements (in order of
listing); the default is to follow the original array order.

The keys of `default_view` are field names which point to a string or
integer, or array thereof. These are *not currently in use*, but are intended
to indicate the values that will be used for a default range when a user makes
a form submission without supplying a range.

For `fields`, the `lang` string property indicates the language code for the
content of this field. It is used for letting the user quickly enable
fields which match their desired locale language(s). It is also used for
setting HTML table column `lang` values to properly display fonts for
languages that leverage it, such as the CJK languages.

One other set of types for `fields` are `name` and `alias` strings (or
`localeKey` pointing to strings). The `name` points to the regular
name for the column, whereas `alias` is currently used to point to an
alias name of the field (more specifically to an alternate name after
converting its values via `fieldvalue-aliases`). The boolean or string
`prefer_alias` property in turn determines whether this alias will be
used by default (currently if it is `false`, it is not used at all).
This functionality might be moved into a plug-in in the future.

Also in `fields`, `fieldvalue-aliases` can after any `localeKey` processing,
be an object of field name keys pointing to an array of alias names (as
strings or integers) or to an object whose keys indicating the type of
alias and string values. This can provide alternate values for what is
actually present in the data table (e.g., replacing numeric codes with
human-readable strings).

For `fields`, the `primary_text_field` and `orig_lang_field` booleans
and `orig_langs` array of string language codes are *not currently in use*
but intended to indicate the main work under consideration (as opposed
to translations), whether it is the source of translations or a target
translation, and what its source languages are (if not itself marked as
an original language).

The `roman` property of `fields` is intended to indicate Roman numerals.
It is *not currently in use* and where not automated from Arabic numerals,
it might be indicated in the future via a language code and more generic
`numeric` property (so as to support multiple non-Arabic counting systems
which are hard-coded as opposed to built via an automated plug-in).

As with other files, there is also a `localization-strings` key object (see the `languages.json` and `locales/en-US.json` section), keyed
to language code, which is keyed to an object of keys (which can be strings
(e.g., `heading` or `alias`), arrays of strings, or other objects of keys,
including specifically for JSON metadata, `fieldnames`, `fieldaliases`,
`fieldvalue`, and `fieldvalue-aliases`).
