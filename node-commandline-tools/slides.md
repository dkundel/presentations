footer: Dominik Kundel ([@dkundel](https://twitter.com/dkundel))
slidenumbers: true
build-lists: true

## Command Line Tools
## with Node.js

^ Hi everyone!

^ First of all thank you for attending my talk today! I'm going to give you an insight today into
how to write command line tools using Node.JS and why it's awesome. But first let me give you a
brief introduction of myself.

---

## About me

I'm a developer with a passion for JavaScript, Hackathons and good Whisky.

![inline 45%](../images/me-neutral.png)

Follow me: [fb.com/dominik402](https://fb.com/dominik402) | [@dkundel](https://twitter.com/dkundel) | [github/dkundel](https://github.com/dkundel)

^ My name is Dominik and I'm a developer with a passion for JavaScript. You'll usually find me on weekends
at hackathons across Europe either hacking or running around as mentor. I also love a good whisky so if you
have a suggestion for a great Bourbon I should try please let me know!

^ Now let's get to the main topic of today and start with an obvious question in the room!

---

## Why command line tools?

^ Why command line tools?
How many of you use the command line on a daily basis in their development flow?
How many of you prefer using Git via the command line versus a GUI?

---

![](http://i.giphy.com/mN2COEBlQRU7S.gif)

# The terminal is badass!

^ In my opinion the terminal is simply badass!
If you haven't tried the command line star wars you should check it out!

---

![](http://i.giphy.com/uWCoUAQvJ3g3K.gif)

## It gives you super powers!

^ It basically gives you super powers!

---

## Command line tools
### **The ultimate productivity tool**

- Build on existing and built-in tools
- Piping (`|`)
- Redirects (`>/<`)
- Chaining (`&&`)
- Parallel execution (`&`)

^ Command line tools are the ultimate productivity tools.
- It's just so easy stich together different existing tools
- pipe the output from one command as input into another tool
- Redirect the output straight into a file
- Or chain a bunch of commands together
- And if you want to save some time you can even execute them in parallel just as easily!

---

## Writing a new script
### **The difficult choice of a language**

^ Probably the first issue you'll face when you decide to write a script to automate a task is:
Which scripting or programming language will I be using? Shell script. If yes is it going to be
Bash, Z-Shell, Fish-Shell? What about Windows support? Compiled or non compiled? Python, Ruby,
C++, C#. Pretty much any language offers the possibility to write a command line tool. Today
I'm going to focus on why I think writing these tools in Node.JS is great!

---

## Why Node.JS?

- Cross-platform
- Leverage over 200k exisiting libraries
- Easy distribution thanks to NPM
- Streams and event driven by design
- Most likely already installed

^ - The first thing is that Node.JS focusses on being cross platform. It runs on Windows just as well as it
  does on Linux or Mac OS. The default modules such as the 'os' and 'fs' are able to handle differences
  in the operating systems
- The other important thing is NPM. You can leverage more than 200k existing modules.
- NPM also allows you to comfortably publish and distribute your modules. And if you don't want to add it
  to the public registry you can leverage private registries or simply install it using the git url.
- Node is event driven by design and uses streams out of the box. Piping data from one stream to another is
  natural for it. And the asynchronous nature is great for handling multiple tasks at once.
- And if you target web developers chances are high that the person already has Node installed. I think even
  Visual Studio asks you during the setup by now if you want to install Node.JS

---

## `unicorn [options] <recipients...>`

- Read content piped into the tool
- Send emails with the content to various recipients

^ Today we are going to build a small tool that we will call unicorn that will do two things:

^ - It will read the input that is being piped into it from Stdin
- and it will send the content that it read to various recipients.

^ Just like every good command-line tool we'll provide the possibility of adding options and print a help
dialog.

---

![](http://i.giphy.com/ZVik7pBtu9dNS.gif)

# Let's
# Code!

---

![](http://i.giphy.com/10i4zvRFfIErWE.gif)

## Consume your new
## command line tool

^ Now that we created a new tool let's talk about how to use it. During the development process we used npm link
to create symbolic links that point to our actual development files. Now obviously that's not the go-to approach

^ Instead npm has two ways we can consume these command line tools.

---

## Install your script globally

```
> npm install unicorn --global
> unicorn --help
  Usage: example [options] <recipients...>

  Options:

    -h, --help            output usage information
    -V, --version         output the version number
    -s, --subject [name]  Subject
```

^ Let's assume for both ways that you went to through the steps to publish your tool to NPM using npm publish.

^ The first way and probably more common way of consuming it is by installing it globally. This is the most
common way you would install a lot of tools such as gulp or grunt. In their API it usually says something
like:

^ run npm install -g gulp-cli

^ However this is not always the best way to work with these tools. By installing tools like this you will
hit issues with things such as versioning. Tools like gulp or grunt have their own way of handling this but
let's look at the TypeScript compiler as an example.

^ In the documentation you'll find that to install the TypeScript compiler you have to run: 'npm install -g typescript'
and it will give you the 'tsc' command. However how do we ensure that you have the same version as
your colleague.

---

## Use your tool in a project

```
> npm install typescript --save-dev
```

Creates an entry:
`node_modules/.bin/tsc`

^ The solution comes straight with NPM and NPM scripts. If you install a tool that has a bin entry and install
it in your project either with --save or --save-dev it will create an entry in node_modules/.bin.

^ You could then obviously go ahead and use the tool like that. But there is a neater way that comes with NPM scripts.

---

## Use your tool in a project

```
{
  ...
  "scripts": {
    "build": "tsc",
    "postinstall": "npm run build"
  }
  ...
}
```

```
> npm run build
```

^ Inside the scripts property of the package.json we can add a varierty of commands and in there we don't have
to care about the full path to the script because the node_modules/.bin directory will be temporarily added
to the Path. This is especially great for Windows since the paths are different than in UNIX.

^ We also have access to the NPM lifecycle which means that we can for example trigger an automatic build after
every npm install.

^ The last thing I'm going to show today is one more cool thing that you can do with these NPM scripts and that
is argument passing.

---

## Pass additional arguments

```
{
  ...
  "scripts": {
    "build": "tsc",
    "build:dev": "npm run build -- --watch",
    "postinstall": "npm run build"
  }
  ...
}
```

```
> npm run build:help
```

^ Let's say we want to define a command 'build' that will build the TypeScript and also a command that will run
it with the compiler run in watch mode to watch for any changes. We can break it up into a 'build' and a
'build:dev' where 'build:dev' will call 'build' but also pass an additional '--watch' to the command in 'build'.

---

## Result: A super easy project setup

```
> git clone https://github.com/project/example.git
> cd example && npm install
```

^ With these things the setup for your project can become as easy as cloning the repository and running 'npm install'.
No need for installing tools such as Bower, Gulp, Grunt or other tools.

---

# Thanks
# for listening!

^ With that said. Thanks for listening everyone!

---

# Questions?

---

### 1. Create a new project
```
> mkdir unicorn && cd $_
> npm init
```

### 2. Change the `package.json`
```
{
  ...
  "bin": {
    "unicorn": "unicorn.js"
  },
  ...
}
```

---

### 3. Install some dependencies
```
> npm install commander nodemailer --save
```

### 4. Create `unicorn.js` and add dependencies
```javascript
const pkg = require('./package.json');
const nodemailer = require('nodemailer');
let program = require('commander');
```

---

### 5. Read piped content from stdin
```javascript
//...
let input = '';
process.stdin.resume();
process.stdin.setEncoding('utf8');
process.stdin.on('data', (data) => {
  input += data;
});

process.stdin.on('end', () => {
  //...
}
//...
```

---

### 6. Read command line arguments
```javascript
//...
program
  .version(pkg.version)
  .option('-s, --subject [text]', 'Subject')
  .parse(process.argv);

console.log('Subject specified: %s', program.subject);
//...
```

---

### 7. Load config
```javascript
//...
const os = require('os');
const config = require(`${os.homedir()}/unicorn.config.json`);
//..
```

---

### 8. Send email
```javascript
//...
let transport = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: config.username,
    pass: config.password
  }
});

transport.sendMail({
  from: config.username,
  to: recipient,
  subject: subject,
  html: input
}, function (err, data) {
  // callback
});
```

---

### 9. Exit the program
```javascript
process.exit(0); // success
process.exit(1); // failure
```

### 10. Color output
```javascript
const chalk = require('chalk');
const error = chalk.bold.error;
//...
console.error('%s Some failure', error('ERROR'));
process.exit(1);
```