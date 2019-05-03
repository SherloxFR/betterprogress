Flexible ascii progress bar.
<img align="right" src="https://cdn.rawgit.com/standard/standard/master/badge.svg">

## Installation

```bash
$ npm install betterprogress
```
for public use
## Usage

First we create a `ProgressBar`, giving it a format string
as well as the `total`, telling the progress bar when it will
be considered complete. After that all we need to do is `tick()` appropriately.

```javascript
var ProgressBar = require('betterprogress');

var bar = new ProgressBar(':bar', { total: 10 });
var timer = setInterval(function () {
  bar.tick();
  if (bar.complete) {
    console.log('\ncomplete\n');
    clearInterval(timer);
  }
}, 100);
```

### Options

These are keys in the options object you can pass to the progress bar along with
`total` as seen in the example above.

- `curr` current completed index
- `total` total number of ticks to complete
- `width` the displayed width of the progress bar defaulting to total
- `stream` the output stream defaulting to stderr
- `head` head character defaulting to complete character
- `complete` completion character defaulting to "="
- `incomplete` incomplete character defaulting to "-"
- `renderThrottle` minimum time between updates in milliseconds defaulting to 250
- `clear` option to clear the bar on completion defaulting to false
- `callback` optional function to call when the progress bar completes

### Tokens

These are tokens you can use in the format of your progress bar.

- `:bar` the progress bar itself
- `:current` current tick number
- `:total` total ticks
- `:elapsed` elapsed time in minutes + seconds / seconds (auto)
- `:percent` completion percentage
- `:eta` estimated completion time in minutes + seconds / seconds (auto)
- `:rate` rate of download in Mbps / Kbps / Bbps (auto)

### Custom Tokens

You can define custom tokens by adding a `{'name': value}` object parameter to your method (`tick()`, `update()`, etc.) calls.

```javascript
var bar = new ProgressBar(':current: :token1 :token2', { total: 3 })
bar.tick({
  'token1': "Hello",
  'token2': "World!\n"
})
bar.tick(2, {
  'token1': "Goodbye",
  'token2': "World!"
})
```
The above example would result in the output below.

```
1: Hello World!
3: Goodbye World!
```

## Examples

### Download

In our download example each tick has a variable influence, so we pass the chunk
length which adjusts the progress bar appropriately relative to the total
length.

```javascript
var ProgressBar = require('progress');
var https = require('https');

var req = https.request({
  host: 'download.github.com',
  port: 443,
  path: '/visionmedia-node-jscoverage-0d4608a.zip'
});

req.on('response', function(res){
  var len = parseInt(res.headers['content-length'], 10);

  console.log();
  var bar = new ProgressBar('  downloading [:bar] :rate :percent :eta', {
    complete: '=',
    incomplete: ' ',
    width: 20,
    total: len
  });

  res.on('data', function (chunk) {
    bar.tick(chunk.length);
  });

  res.on('end', function () {
    console.log('\n');
  });
});

req.end();
```

The above example result in a progress bar like the one below.

```
downloading [=====             ] 39/bps 29% 3.7s
```

### Interrupt

To display a message during progress bar execution, use `interrupt()`
```javascript
var ProgressBar = require('progress');

var bar = new ProgressBar(':bar :current/:total', { total: 10 });
var timer = setInterval(function () {
  bar.tick();
  if (bar.complete) {
    clearInterval(timer);
  } else if (bar.curr === 5) {
      bar.interrupt('this message appears above the progress bar\ncurrent progress is ' + bar.curr + '/' + bar.total);
  }
}, 1000);
```

You can see more examples in the `examples` folder.

## License

MIT