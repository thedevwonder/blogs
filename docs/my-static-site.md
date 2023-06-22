# An attempt to create a Static Site Generator - 1

It's a week before the JSConf India 2023, and I am getting a site ready. Now I can go on this venture in more ways than I can count, but I was inspired by the minimilism of [Notion](https://www.notion.so/) and had a bad experience with SSGs(Static Site Generators).

The problem that I was solving was quite personal. I wanted to document my learnings, a personal space where I'll put out my observations and probably learn something out of the process. Blogging websites are public and I do not feel the need to make them my online diary. Static Site Generators are probably the best for the task, but I find some ways of making it quite obsolete. Too much setup, even though it may be a copy of some other website. 

Another problem was project maintainability and easily customizable themes and components.

I find markdown a very good and efficient way to write and format texts, and it is a pretty popular formatting language. I imagined if I could somehow integrate markdown with React, as I could then convert markdown texts to html elements and add custom components I create with React. If you break down a doc to a level, you see everything is a lego block, placed from top to bottom. 

I didn't explore the React tools for initializing the project. Why? I wanted to add only the files I would need in the project and so I planned on creating the project from scratch. I installed webpack, babel and react to get going. Here are few references I used:

 - [Create React App without Create React App](https://blog.bitsrc.io/create-react-app-without-create-react-app-b0a5806a92)
 - [Create React App from Scratch like a Pro](https://dev.to/ruppysuppy/create-react-app-from-scratch-like-a-pro-de0)
 - [Create a React App from Scratch in 2021](https://javascript.plainenglish.io/create-a-react-app-from-scratch-in-2021-8e9948602e9c)

Pretty convenient for the first time.

Next I was researching on how to convert my markdown text to html and found a cool package for this. The [Showdownjs](https://showdownjs.com/).
Now this tool will take the markdown text and convert it into html element. But there are two problems:

 1. One good application of this was the live markdown to html editor, in which the html string is added to the 
 `document.getElementById('some-id').innerHTML`. But we are writing JSX here. I found it dangerous to add html that way at scale using `dangerouslySetInnerHTML`. Refer:
 [https://stackoverflow.com/questions/37337289/react-js-set-innerhtml-vs-dangerouslysetinnerhtml](https://stackoverflow.com/questions/37337289/react-js-set-innerhtml-vs-dangerouslysetinnerhtml)
 2. I need texts from markdown file to be converted into a string. And so I had to find a way to do that using code.

For the first part, I was planning on doing mdx integration and then probably transpiling it back to md in the run-time, but I wanted to preserve the md format in Github so that Github formats my md files too. So I used [html-react-parser](https://www.npmjs.com/package/html-react-parser) instead. This would now parse my html string into a React element.

For the second part, I used [raw-loader](https://www.npmjs.com/package/raw-loader) which would extract the texts from md files. I would then feed it to showdownjs, which would give me the HTML string.

The dots seem pretty connected now. However, we are still a long way to go. I am breaking down my idolized version to a pilot version of the web-app I want to build. In an idolized picture, I would just have to create md files in a folder and use some logic to read all the files from the folder and dynamically extract texts from the file, converting them into html.

But we have two more problems here,

 1. We are using React, an SPA, however we would need different html files for each markdown and route them appropriately.
 2. Dynamically importing the text from the file and creating a tree out of it with directories and sub-directories, feels complex. Does it simplify my process of documenting my learnings by restricting too much on how to structure my folders?

I could definitely use some ideas here at this stage. However, since I also wanted to get my site ready before the JS Conference, I thought I should break down my site to a simpler version.

 1. I would add my blogs on the home page itself, putting everything on the same page and dividing it by element ids.
 2. I would manually import my markdown files, create an array of file properties and use that to dynamically generate html files and unqiue links.

I created a production build using webpack and deployed my site to Github pages. I also had a domain under my name so I used it to custom set my domain. It failed to load the site SSL security. I then went to my last resort and deployed it on Netlify. However, I had to change my namespace to that of Netlify which caused my previous sites running on sub-domain to stop working. Too much trouble.

You can find the rest of the code pretty much here, but I prepared the simplest version of what I was supposed to make and got it ready before the JS Conf.

[Github](https://github.com/thedevwonder/yashasva)

The story hasn't ended yet, and so I'll see you in the next part...