title: CSS Counters
date: 2016-01-25 12:23:35
desc: css counter basic knowledge and examples
---

Several years ago, a question was rasied by someone on Weibo: how to code the module of calculating commodity price, which plays a big role in Taobao commodity detail pages. Usually at the detail page, the commodity has many options, such like size and color. The amount will change real time when users change their options. This question is not noly business-oriented, but also has many solving methods. JS came to my mind first at the time.

The last two weeks, I'm digesting technical posts which collected in Pocket and Weibo over past three years. There are many Best Practice in it, such like CSS Counters which the rest is going to say. 

<!-- more -->

CSS Counters is proposed in CSS2.1, so not only modern browsers, but also IE8+ support it. CSS Counters is a auto-counter, and it should coordinate with pseudo-classes `::before` and `::after` to use。

## Property

CSS Counters have a series of property or method to use:

- `counter-reset`, used to initialize a counter. It receives two parameters: the first is a string identifier, and the second is a optional number which used to initialize counter's default value.   
- `counter-increment`, used to increse the counter's value, and it receives a optional number parameter. If the optional parameter exsits, the counter will plus it instead of the default value one.
- `counter()`, used to get counter's result in the `content` property.

## Usage 

As metioned above, we could use counter-reset property to defined a counter. Look at the following html structure:

```html
<body>
    <ul>
        <li></li>
        <li></li>
        <li></li>
    </ul>
    <p></p>
</body>
```

And styles:

```scss
ul {
    counter-reset: li-counter 10;
    li {
        counter-increment: li-counter 2;
    }
    p::before {
        content: counter(li-counter);
    }
}
```

Based on above code fragments, counter called "li-counter" was created, and its initial value is 10. At the li tag, counter-increment comes in and increse the counter's vlaue. Finally, p tag was created with pseudo-class `::before` to display the counter's end result.

## Example

<div class="counter-wrap"><ul class="row"><li class="price-10"><input type="radio" name="color" id="white"/><label for="white">White(+10)</label></li><li class="price-13"><input type="radio" name="color" id="red"/><label for="red">Red(+13)</label></li><li class="price-15"><input type="radio" name="color" id="green"/><label for="green">Green(+15)</label></li></ul><ul class="row"><li class="price-4"><input type="radio" name="size" id="small"/><label for="small">Small(+4)</label></li><li class="price-8"><input type="radio" name="size" id="large"/><label for="large">Large(+8)</label></li></ul><ul class="row"><li class="price-total"></li></ul></div>

<style>
@charset "UTF-8";
.counter-wrap {
  width: 360px;
  margin: 0 auto;
  counter-reset: price 10;
}

.counter-wrap ul.row {
  margin: 2px 2px 2px 0;
  padding: 0;
  list-style-type: none;
}

.counter-wrap li {
  display: inline-block;
  margin: 10px 2px;
  text-align: center;
}

.counter-wrap input {
  position: absolute;
  clip: rect(0 0 0 0);
}

.counter-wrap label {
  padding: 10px 16px;
  color: white;
  background-color: #79BD8F;
  border-radius: 5px;
}

.counter-wrap input:checked + label {
  background-color: #F06161;
}

.counter-wrap input#small:checked {
  counter-increment: price 4;
}

.counter-wrap input#large:checked {
  counter-increment: price 8;
}

.counter-wrap input#white:checked {
  counter-increment: price 10;
}

.counter-wrap input#red:checked {
  counter-increment: price 13;
}

.counter-wrap input#green:checked {
  counter-increment: price 15;
}

.counter-wrap li.price-total {
  padding: 10px 16px;
  background-color: #BEEB9F;
  border-radius: 5px;
}
.counter-wrap li.price-total::before {
  content: "$" counter(price);
  color: #D83C65;
  font-weight: bold;
}
</style>

As you can see, here is a simple commodity form with multiple options. If you click colors or sizes, the amount will change real time. With this approach, code is cleaner and more robust.

For more infomation about this example, you could look over my Codepen playground via this [link](http://codepen.io/pinggod/pen/JGpVNx?editors=1100).

