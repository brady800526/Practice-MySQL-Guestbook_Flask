## Summary

This repo is a practice of using Python Flask Framework combined with MySQL database to build a simple guestbook app.
This app is aimed to save guests' comments when guest press save button, and show all the saved results on the landing page.

*All the step is followed by [youtube channel](https://www.youtube.com/watch?v=mD4KFDmusCc&list=PLXmMXHVSvS-AjwTOtiW1DXFYTgUlrUmHV) created by Pretty Printed.*

## MySQL database

MySQL database is the most common representative database using SQL (Structure Query Language), and we gonna use this database to accomplish this simple app.

Id you want to know more in SQL, here is a link refers to SQL example in this [website](http://www.codedata.com.tw/database/mysql-tutorial-6-crud-maintenance/) writen in Traditional Chinese.

## Usage

Download the repo, and under the directory run:

    python guestbook.py

You will see the landing page.

<figure style="text-align: center;">
    <img src="http://i63.tinypic.com/23uecs3.png" alt="Predict PM2.5 Example" style="width: 80%; height: 80%"/>
    <figcaption>Landing Page</figcaption>
</figure>

By clicking the **Sign the Guest Book** Button, guest can Sign on the Guest Book with Name and Comment (With no Validation).

<figure style="text-align: center;">
    <img src="http://i65.tinypic.com/2rrp4eq.png" alt="Predict PM2.5 Example" style="width: 80%; height: 80%"/>
    <figcaption>SignUp Page</figcaption>
</figure>

By entering name "Brady" and comment "This is my first comment" and press the Sign button, guest will back to the landing page with new comment show below the heading.

<figure style="text-align: center;">
    <img src="http://i67.tinypic.com/24ee3qe.png" alt="Predict PM2.5 Example" style="width: 80%; height: 80%"/>
    <figcaption>Landing Page</figcaption>
</figure>

You can easily upload new comment to the guestbook, and see all the comments on the landing page.

## Code

All the logic are placed in the guestbook.py, and static and templates are simple file.

In the landing page (index.html), we can click the link to a sing up page (sign.html). Then, after finish the form and press 【Sign】, we will send a post request to '/process' route. This route will save the name and comment to database and redirect the guest to the landing page (index.html). Finally, we can see your comment on the landing page and other saved comments.
<figure style="text-align: center;">
    <img src="http://i63.tinypic.com/vzzyhz.jpg" alt="Predict PM2.5 Example" style="width: 80%; height: 80%"/>
    <figcaption>The architecture of this app</figcaption>
</figure>

#### Setting
------------------------------------------------------------------------

guestbook.py
```
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:root@localhost:3306/flask_guestbook'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)
```

First we start an app wrap by Flask Framework by `app = Flask(__name__)`

In order to connect to the database, we specify the database path with prefix `mysql+pymysql://` Put the database username and password `root:root` Followed by hostname, port and MySQL table name `localhost:3306/flask_guestbook`

Then we can initialize the db by `db = SQLAlchemy(app)`

(Optional) Disable unneccessary warning by `app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False`

#### Schema Design
------------------------------------------------------------------------

guestbook.py
```
class Comments(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))
    comment = db.Column(db.String(1000))
```

if column will be the primary key with data type Integer.
name and comment column will be the data type String with 20 and 1000 characters.

#### index route - Show the Landing Page
------------------------------------------------------------------------

guestbook.py
```
@app.route('/')
def index():
    result = Comments.query.all()
    return render_template('index.html', result=result)
```

`Comment.query.all()` will fetch all the data from Comments table.
Use `render_remplate` will go the the specified page, and we can send variables in the paraenthesis after first param.

In the route we will send all the comments queried from comments table to index.html page.

#### sign route - Show the Sing Up Page
------------------------------------------------------------------------

guestbook.py
```
@app.route('/sign')
def sign():
    return render_template('sign.html')
```

#### process route - Process the comment and save to db
------------------------------------------------------------------------

guestbook.py
```
@app.route('/process', methods=['POST'])
def process():
    name = request.form['name']
    comment = request.form['comment']

    signature = Comments(name=name, comment=comment)
    db.session.add(signature)
    db.session.commit()

    return redirect(url_for('index'))
```

We will receive the name and comment from the form POST request.

Save the comment by `Comments(name=name, comment=comment)` as signature, put the comment to database to db with `db.sesstion.add(signature)` followed by `db.sesstion.commit`. 
In order to see all the results, we have to query all the comments and print on the landing page. 
We already to index() view function have already to this, so we can use `redirect(url_for(index))` to go back to index view function.
