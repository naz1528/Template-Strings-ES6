# Template-Strings-ES6
                                         Getting Literal With ES6 Template Strings
                                         
Strings in javacSript have been historically limited. However, template strings in es6 has changed that.They introduce a way to define strings with domain-specific languages (DSLs), bringing better:

                                            String interpolation
                                            Embedded expressions
                                            Multiline strings without hacks
                                            String formatting
                                            String tagging for safe HTML escaping, localization and more.

Syntax:

         Template Strings use back ticks rather than single or double quotes.
          var str = `template string`;
          
          
                                               String Substitution
example: simple string substitution :-

                                    var name = `Kevin`;
                                    console.log(${name})
          
          
As all string substitutions in Template Strings are JavaScript expressions, we can substitute a lot more than variable names. For example, below we can use expression interpolation to embed for some readable inline math:

                                  var a = 10;
                                  var b = 10;
                                  console.log(`JavaScript first appeared ${a+b} years ago. Crazy!`);

                          //=> JavaScript first appeared 20 years ago. Crazy!

                        console.log(`The number of JS MVC frameworks is ${2 * (a + b)} and not ${10 * (a + b)}.`);
                        //=> The number of JS frameworks is 40 and not 200.
They are also very useful for functions inside expressions:

                                function fn() { return "I am a result. Rarr"; }
                                console.log(`foo ${fn()} bar`);
                                //=> foo I am a result. Rarr bar.
                                The ${} works fine with any kind of expression, including member expressions and method calls:

                                var user = {name: 'Caitlin Potter'};
                                console.log(`Thanks for getting this into V8, ${user.name.toUpperCase()}.`);

                      // => "Thanks for getting this into V8, CAITLIN POTTER";

                      // And another example
                      var thing = 'drugs';
                      console.log(`Say no to ${thing}. Although if you're talking to ${thing} you may already be on ${thing}.`);

              // => Say no to drugs. Although if you're talking to drugs you may already be on drugs.
If you require backticks inside of your string, it can be escaped using the backslash character \ as follows:

                  var greeting = `\`Yo\` World!`;
Multiline Strings
Multiline strings in JavaScript have required hacky workarounds for some time. Current solutions for them require that strings either exist on a single line or be split into multiline strings using a \ (blackslash) before each newline. For example:

                        var greeting = "Yo \
                        World";
Whilst this should work fine in most modern JavaScript engines, the behavior itself is still a bit of a hack. One can also use string concatenation to fake multiline support, but this equally leaves something to be desired:

                            var greeting = "Yo " +
                            "World";
Template Strings significantly simplify multiline strings. Simply include newlines where they are needed and BOOM. Here's an example:

Any whitespace inside of the backtick syntax will also be considered part of the string.

                          console.log(`string text line 1
                          string text line 2`);
Tagged Templates
So far, we've looked at using Template Strings for string substitution and for creating multiline strings. Another powerful feature they bring is tagged templates. Tagged Templates transform a Template String by placing a function name before the template string. For example:

                        fn`Hello ${you}! You're looking ${adjective} today!`
The semantics of a tagged template string are very different from those of a normal one. In essence, they are a special type of function call: the above "desugars" into

                    fn(["Hello ", "! You're looking ", " today!"], you, adjective);
Note: Nicholas Zakas goes into more detail on the break-down of these arguments in the Template Strings section of his excellent book, Understanding ES6.
                    Note how the (n + 1)th argument corresponds to the substitution that takes place between the nth and (n + 1)th entries in the string array. This can be useful for all sorts of things, but one of the most straightforward is automatic escaping of any interpolated variables.

For example, you could write a HTML-escaping function such that..

                      html`<p title="${title}">Hello ${you}!</p>`
returns a string with the appropriate variables substituted in, but with all HTML-unsafe characters replaced. Let’s do that. Our HTML-escaping function will take two arguments: a username and a comment. Both may contain HTML unsafe characters (namely ', ", <, >, and &). For example, if the username is "Domenic Denicola" and the comment is "& is a fun tag", we should output:

<b>Domenic Denicola says:</b> "&amp; is a fun tag"
Our tagged template solution could thus be written as follows:

// HTML Escape helper utility
                                                            var util = (function () {
                                                              // Thanks to Andrea Giammarchi
                                                              var
                                                                reEscape = /[&<>'"]/g,
                                                                reUnescape = /&(?:amp|#38|lt|#60|gt|#62|apos|#39|quot|#34);/g,
                                                                oEscape = {
                                                                  '&': '&amp;',
                                                                  '<': '&lt;',
                                                                  '>': '&gt;',
                                                                  "'": '&#39;',
                                                                  '"': '&quot;'
                                                                },
                                                                oUnescape = {
                                                                  '&amp;': '&',
                                                                  '&#38;': '&',
                                                                  '&lt;': '<',
                                                                  '&#60;': '<',
                                                                  '&gt;': '>',
                                                                  '&#62;': '>',
                                                                  '&apos;': "'",
                                                                  '&#39;': "'",
                                                                  '&quot;': '"',
                                                                  '&#34;': '"'
                                                                },
                                                                fnEscape = function (m) {
                                                                  return oEscape[m];
                                                                },
                                                                fnUnescape = function (m) {
                                                                  return oUnescape[m];
                                                                },
                                                                replace = String.prototype.replace
                                                              ;
                                                              return (Object.freeze || Object)({
                                                                escape: function escape(s) {
                                                                  return replace.call(s, reEscape, fnEscape);
                                                                },
                                                                unescape: function unescape(s) {
                                                                  return replace.call(s, reUnescape, fnUnescape);
                                                                }
                                                              });
                                                            }());

                                                            // Tagged template function
                                                            function html(pieces) {
                                                                var result = pieces[0];
                                                                var substitutions = [].slice.call(arguments, 1);
                                                                for (var i = 0; i < substitutions.length; ++i) {
                                                                    result += util.escape(substitutions[i]) + pieces[i + 1];
                                                                }

                                                                return result;
                                                            }

                var username = "Domenic Denicola";
                var tag = "& is a fun tag";
                console.log(html`<b>${username} says</b>: "${tag}"`);
                //=> <b>Domenic Denicola says</b>: "&amp; is a fun tag"


If you are using template literals only with placeholders (e.g. ${expression}) like in the question's example, then the result is the same as just concatenating strings. Subjectively it looks better and is easier to read, especially for multi-line strings or strings containing both ' and " since you don't have to escape those characters any more.

Readability is a great feature, but the most interesting thing about templates are Tagged template literals:

let person = {name: 'John Smith'}; 
let tag = (strArr, name) => strArr[0] + name.toUpperCase() + strArr[1];  
tag `My name is ${person.name}!` // Output: My name is JOHN SMITH!
In the third line of this example, a function named tag is called. The content of the template string is split into multiple variables, that you can access in the arguments of the tag function: literal sections (in this example My name is and !) and substitutions (John Smith). The template literal will be evaluated to whatever the tag function returns. (in this case)

The ECMAScript wiki lists some possible use cases, like automatically escaping or encoding input, or localization. You could create a tag function named msg that looks up the literal parts like My name is and substitutes them with translations into the current locale's language, for example into German:

console.log(msg`My name is ${person.name}.`) // Output: Mein Name ist John Smith.
The value returned by the tag function doesn't even have to be a string. You could create a tag function named $ which evaluates the string and uses it as a query selector to return a collection of DOM nodes, like in this example:

$`a.${className}[href=~'//${domain}/']

One really nice benefit of interpolated string literals is they are allowed to split across multiple lines:

var Actor = {"name" : "RajiniKanth"};

var text =
`Now is the time for all good men like ${Actor.name}
to come to the aid of their
country!`;
console.log( text );
// Now is the time for all good men
// to come to the aid of their
// country!
