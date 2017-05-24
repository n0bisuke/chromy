# Chromy

Chromy is a library for operating headless chrome. 

## Installation

```bash
npm i chromy
```

## Usage

```js
const Chromy = require('chromy')

// not headless
// let chromy = new Chromy({visible:true})
let chromy = new Chromy()
chromy.chain()
      .goto('http://example.com/')
      .evaluate(() => {
        return document.querySelectorAll('*').length
      })
      .result((r) => console.log(r))
      .end()
      .then(() => chromy.close())
```

You can also use async/await interfaces like this:

```js
const Chromy = require('chromy')

async function main () {
  let chromy = new Chromy()
  await chromy.goto('http://example.com/')
  const result = await chromy.evaluate(() => {
          return document.querySelectorAll('*').length
        })
  console.log(result)
  await chromy.close()
}

main()
```

## API

##### Chromy(options)

###### options  

visible(default: false): If set to true, chrome is launched in visible mode.  
port(default: 9222): --remote-debugging-port  
waitTimeout(default: 30000): If wait() does not be finished in a specified time WaitTimeoutError will be throwed.  
gotoTimeout(default: 30000): If goto() does not be finished in a specified time GotoTimeoutError will be throwed.  
evaluateTimeout(default: 30000): If evaluate() does not be finished in a specified time EvaluateTimeError will be throwed.

##### .goto(url, options)

###### options

waitLoadEvent(default: true): If set to false, goto() doesn't wait until load event is fired.

##### .waitLoadEvent()

wait until a load event is fired.

##### .forward()

go forward to the next page and wait until load event is fired.

##### .back()

go back to the previous page and wait until load event is fired.

##### .evaluate(func)

##### .result(func)

result() receive a result of previous directive.

```js
chromy.chain()
      .goto('http://example.com')
      .evaluate(() => {
        return document.querySelectorAll('*').length
      })
      .result((length) => {
        // length is a result of evaluate() directive.
        console.log(length)
      }
      .end()
```

##### .end()

##### .wait(msec)

alias for .sleep(msec)

##### .wait(selector)

wait until selector you specified appear in a DOM tree.

##### .wait(func)

wait until function you supplied is evaluated as true.

##### .sleep(msec)

wait for milli seconds you specified.

##### .type(selector, text)

##### .insert(selector, text)

##### .check(selector)

##### .uncheck(selector)

##### .select(selector, value)

##### .click(selector, options)

###### options

waitLoadEvent(Default:false): If set to true, wait until load event is fired after click event is fired.

##### .defineFunction(func)

```js
function outerFunc () {
  return 'VALUE'
}
chromy.chain()
      .goto('http://example.com')
      .defineFunction(outerFunc)
      .evaluate(() => {
        outerFunc()
      })
      .end()
```

##### .screenshot(format = 'png', quality = 100, fromSurface = true)

It export a current screen as an image data. 
`format` must be eather 'png' or 'jpeg'.

See examples: [examples/screenshot.js]

##### .pdf()

It export a current screen as a PDF data. 

See examples: [examples/screenshot.js]

##### .console(func)

```js
chromy.chain()
      .goto('http://example.com')
      .console((text) => {
        console.log(text)
      })
      .evaluate(() => {
        console.log('HEY')
      })
      .end()
```

##### .receiveMessage(func)

receive a message from browser.  

You can communicate with a browser by using receiveMessage() and sendToChromy().
sendToChromy() is a special function to communicate with Chromy.
When you call receiveMessage() at the first time, sendToChromy() is defined in a browser automatically.
A listner function passed to receiveMessage() receives parameters when sendToChromy() is executed in a browser.


```js
chromy.chain()
      .goto('http://example.com')
      .receiveMessage((msg) => {
        console.log(msg[0].value)
      })
      .evaluate(() => {
        sendToChromy({value: 'foo'})
      })
```

##### static cleanup()

close all browsers.

```js
process.on('SIGINT', async () => {
  await Chromy.cleanup()
  process.exit(1)
})
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/OnetapInc/chromy

