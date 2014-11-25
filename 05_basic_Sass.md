##Basic Sass(SCSS)<br>
Created by [Will Allen, Painted Ltd]
<br>
<br>
First rename /assets/stylesheets/application.css to /assets/stylesheets/application.css.scss<br>
Then in /views/layouts/application.html.erb add:<br>
```
<nav> (before all navigation links)
</nav> (after the navigation links)
```
In /assets/stylesheets/application.css.scss add: <br>
```
$nicered: #f55;

.button {
	background: $nicered;
	color: #fff;
	font-weight: bold;
	padding: 10px;
	display: inline-block;
	text-decoration: none;
}

body {
	margin: 0;
	font-family: Helvetica Neue;
}

nav {
	background: #eee;
	padding: 10px;

	a {
		@extend .button;
	}
}

a {
	color: $nicered;
}
```
The only difference that this has given is that the a (links) style can be nested in the class and variables, and extend classes can be created.<br>
Commit to Github



[Will Allen, Painted Ltd]:https://github.com/painted