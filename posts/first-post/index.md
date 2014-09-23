title: First Post
tags: introduction
url: first-post
date: Tue Sep 23 2014 00:00:00 GMT-0400 (EDT)
------------------------
It's been a while that I wish to start blogging. I've wrote a tool called [PressJS](https://github.com/amir-s/pressjs) like one year ago to start creating and writing a blog using that tool. But I failed due to some reasons.

Well, done is better than perfect; So I'm going to start blogging anyway using [PressJS](https://github.com/amir-s/pressjs) while I'm developing it.

[PressJS](https://github.com/amir-s/pressjs) is not going to be YABP (Yet Another Blogging Platform). This is just a personal project that I want to develope and it's main goal is to be minimalistic and dead simple. It has a few features now, but it will be developed and more features will be added very soon.

If you are interested in using and developing it, feel free to fork and send pull-requests.

In order to user `pressjs`, you must have NodeJS installed first. Then install `pressjs` from `npm`:

	npm install pressjs -g


And you are all set! Create a folder for your blog:

	mkdir blog && cd blog


Use this command to initialize your blog:

	pressjs init


With this command, `pressjs` will copy static files for the blog theme, and a config file named `blog.js` in the working directory.
Edit `blog.js` with a text editor and fill your desired values. Remember you should have a trailing `/` for your URL. All the URLs of your blog, will be joined with this URL.

For your first post, create a file named `new.md`: (the `^D` is the end of input character: `ctrl`+`D`)

	touch new.md
	cat > new.md
	Title: My first post
	tags: first, introduction, pressjs
	-------------------------------------------
    Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
	
		Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
	
	^D

Now it is time to build this post:

	pressjs build new.md
	
You can test your blog by starting a local web server:

	pressjs test

Navigate your browser to `http://localhost:3000` and test your blog. Remember, the `pressjs test` command, changes the URL of your blog in the entire files to `localhost:3000`. You should run `pressjs update` again after your tests, before uploading your blog.

`pressjs update` is also useful when you change your `blog.js` config file or delete some posts in `./posts/` folder. Make sure you run `pressjs update` to make the changes.

Have fun.