# sifter.js
[![NPM version](https://badge.fury.io/js/sifter.png)](http://badge.fury.io/js/sifter)
[![Build Status](https://travis-ci.org/brianreavis/sifter.js.png?branch=master)](https://travis-ci.org/brianreavis/sifter.js)

Sifter is a client and server-side library (via [UMD](https://github.com/umdjs/umd)) for textually searching arrays and hashes of objects by property – or multiple properties. It's designed specifically for autocomplete. The process is three-step: *score*, *filter*, *sort*.

* **Supports díåcritîçs.**<br>For example, if searching for "montana" and an item in the set has a value of "montaña", it will still be matched.
* **Smart sorting.**<br>Items are scored intelligently depending on where a match is found in the string (how close to the beginning) and what percentage of the string matches.

```sh
$ npm install sifter # node.js
$ bower install sifter # browser
```

## Usage

```js
var sifter = new Sifter([
	{title: 'Annapurna I', location: 'Nepal', continent: 'Asia'},
	{title: 'Annapurna II', location: 'Nepal', continent: 'Asia'},
	{title: 'Annapurna III', location: 'Nepal', continent: 'Asia'},
	{title: 'Eiger', location: 'Switzerland', continent: 'Europe'},
	{title: 'Everest', location: 'Nepal', continent: 'Asia'},
	{title: 'Gannett', location: 'Wyoming', continent: 'North America'},
	{title: 'Denali', location: 'Alaska', continent: 'North America'}
]);

var result = sifter.search('anna', {
	fields: ['title', 'location', 'continent'],
	sort: [{field: 'title', direction: 'asc'}],
	direction: 'desc',
	limit: 3
});
```

Seaching will provide back meta information and an "items" array that contains objects with the index (or key, if searching a hash) and a score that represents how good of a match the item was. Items that did not match will not be returned.

```
{"score": 0.2878787878787879, "id": 0},
{"score": 0.27777777777777773, "id": 1},
{"score": 0.2692307692307692, "id": 2}
```

The sorting options are only acknowledged when searching by an empty string. Otherwise, sorting will always be by best-match. The full result comes back in the format of:

```js
{
	"options": {
		"fields": ["title", "location", "continent"],
		"sort": [
			{"field": "title", "direction": "asc"}
		],
		"direction": "desc",
		"limit": 3
	},
	"query": "anna",
	"tokens": [{
		"string": "anna",
		"regex": /[aÀÁÂÃÄÅàáâãäå][nÑñ][nÑñ][aÀÁÂÃÄÅàáâãäå]/
	}],
	"total": 3,
	"items": [
		{"score": 0.2878787878787879, "id": 0},
		{"score": 0.27777777777777773, "id": 1},
		{"score": 0.2692307692307692,"id": 2}
	]
}
```

### API

#### #.search(query, options)

Performs a search for `query` with the provided `options`.

<table width="100%">
	<tr>
		<th align="left">Option</th>
		<th>Type</th>
		<th align="left" width="100%">Description</th>
	</tr>
	<tr>
		<td valign="top">"fields"</td>
		<td valign="top">array</td>
		<td valign="top">An array of property names to be searched.</td>
	</tr>
	<tr>
		<td valign="top">"limit"</td>
		<td valign="top">integer</td>
		<td valign="top">The maximum number of results to return.</td>
	</tr>
	<tr>
		<td valign="top">"sort"</td>
		<td valign="top">array</td>
		<td valign="top">An array of fields to sort by. Each item should be an object containing at least a "field" property. Optionally, "direction" can be set to "asc" or "desc". The order of the array defines the sort precedence.<br><br>Unless present, a special "$score" property will be automatically added to the beginning of the sort list. This will make results sorted primarily by match quality (descending).</td>
	</tr>
	<tr>
		<td valign="top">"filter"</td>
		<td valign="top">boolean</td>
		<td valign="top">If <code>false</code>, items with a score of zero will not be filtered out of the result-set.</td>
	</tr>
</table>

## CLI

![CLI](http://i.imgur.com/fSQBnWZ.png)

Sifter comes with a command line interface that's useful for testing on datasets. It accepts JSON and CSV data, either from a file or from stdin (unix pipes). If using CSV data, the first line of the file must be a header row.
```sh
$ npm install -g sifter
```

```sh
$ cat file.csv | sifter --query="ant" --fields=title
$ sifter --query="ant" --fields=title --file=file.csv
```

## Contributing

The following dependencies are required to build and test:

```sh
$ npm install -g mocha
$ npm install -g uglify-js
```

First build a copy with `make` then run the test suite with `make test`.

When issuing a pull request, please exclude "sifter.js" and "sifter.min.js" in the project root.

## License

Copyright &copy; 2013 [Brian Reavis](http://twitter.com/brianreavis) & [Contributors](https://github.com/brianreavis/sifter.js/graphs/contributors)

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at: http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.