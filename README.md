# Source code for maximelafarie.com
Based on the awesome [Todd Motto's Jekyll website](https://github.com/toddmotto/toddmotto.github.io).

## Build this project

### Install jekyll

Ensure `jekyll` is installed:

```
gem install jekyll
```

If you need XCode dependencies, follow the [Jekyll installation guide](https://jekyllrb.com/docs/installation/). For Windows, follow the [Windows installation guide](https://jekyllrb.com/docs/windows/#installation).

Uncompiled [Jekyll](//jekyllrb.com) source code for [maximelafarie.com](//maximelafarie.com).

### Installing dependencies

This project makes use of `gulp`. First, you'll need to make sure you have `gulp` installed:

```
npm install --global gulp
```

Next, you'll need to `npm install` the other dev-dependencies, run this from the `maximelafarie.github.io` root folder:

```
cd maximelafarie.github.io
npm install
```

### Running the server

Gulp is setup to make it easier to run all the tasks, to run the project simply run:

```
npm start
```

This will start serving the project from `localhost:4000`, with livereload functionality.
