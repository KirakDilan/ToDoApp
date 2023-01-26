# ToDoApp
from flask import Flask, render_template, request, redirect, url_for, flash, session, logging
from flask_sqlalchemy import SQLAlchemy // Diese Zeilen muss ganz am Anfang des files stehen

************************************************************
* Vorname , Nachname:   Dilan Kirak                          
 * Studiengang: Wirtschaftsinformatik                        
 * Martikelnummer:    77211925676                            
 * Ort: HWR- Hochschule für Wirtschaft und Recht             
 * @author Dilan	            	                         
 *                                             
* Vorname , Nachname:   Natasza Alexandra Kopka              
 * Studiengang: Wirtschaftsinformatik                         
 * Martikelnummer:    77211880478                             
 * Ort: HWR- Hochschule für Wirtschaft und Recht             
 * @author Natazca	            	                         
 *                                             
* Vorname , Nachname:   Christina Eduardo Maria Kademba       
 * Studiengang: Wirtschaftsinformatik                         
 * Martikelnummer:     7721188143                             
 * Ort: HWR- Hochschule für Wirtschaft und Recht             
 * @author Christina	            	     

 *************************************************************

* THEMA
Hierbei handelt es sich um eine Webseite einer ToDo-Liste. Beim Öffnen der Webseite sieht man als erstes die Loginseite. Der Benutzer kann sich hier einloggen und somit ein eigenes Profil erstellen. Auf dieser Profilseite hat der Nutzer die Möglichkeit einen Nutzernamen, E-Mail & Geburtsdatum zu hinterlegen. Ebenfalls hat man ein Überblick über die Anzahl von ToDos. Nach der Anmeldung wird man auf die Home-Seite weitergeleitet. In diesem Bereiche haben wir unsere Vorteile aufgelistet. 
Auf der ToDo-Seite hat man die Möglichkeit ToDo's einzufügen, upzudaten und zu löschen. Der Nutzer trägt seine ToDo in das Textfeld ein und drückt auf "Add", dann wird diese ToDo in die Liste aufgenommen. Sobald diese Aufgabe erledigt ist, drückt der Nutzer auf "Ubdate" und die Aufgabe wird als "completed" makiert. Durch das drücken von "Delete" wird die Aufgabe aus die Liste gestrichen. 
Zu guter letzt haben wir noch einen Impressum-Bereich mit unseren Angaben. 

************************************************************ 
* ...

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
app.config['SECRET_KEY'] = 'secretkey'

db = SQLAlchemy(app)


******************************************************************
* In der Prozedur haben wir eine Klasse, welche dem User eine Login-Basis zu Verfügung stellt. 


class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), nullable=False, unique=True)#kann nicht leer sein
    password = db.Column(db.String(80), nullable=False)#kann nicht leer sein

@app.route("/")
def home():
    return render_template("SignIn.html")

@app.route("/SignIn",methods=["GET", "POST"])
def login():
    if request.method == "POST":
        uname = request.form["uname"]
        passw = request.form["passw"]
        
    login = User.query.filter_by(username=uname, password=passw).first()
    if login is not None:
        return redirect(url_for("index"))
    return render_template("index.html", login=login)


****************************************************************
* In der Prozedur haben wir eine Klasse, welche dem User eine ToDo-Basis zu Verfügung stellt. Hier haben wir die ID als Integer, den Title als String und die "Complete" als Boolean.  


class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100))
    complete = db.Column(db.Boolean)


@app.route("/ToDos.html")
def todo():
    todo_list = Todo.query.all()
    return render_template("ToDos.html", todo_list=todo_list)
    //koordiniert Sie auf die ToDos.html


******************************************************************
* In der Methode haben wir als Default-Wert "POST". Hier haben wir den Add-Bereich, welches dem User die Möglichkeit gibt ein neues ToDo einzufügen, zu updateten und zu löschen. 


@app.route("/add", methods=["POST"])
def add():
    title = request.form.get("title")
    new_todo = Todo(title=title, complete=False)
    db.session.add(new_todo)
    db.session.commit()
    return redirect(url_for("todo"))

#Todos updaten
@app.route("/update/<int:todo_id>")
def update(todo_id):
    todo = Todo.query.filter_by(id=todo_id).first()
    todo.complete = not todo.complete
    db.session.commit()
    return redirect(url_for("todo"))

#Todos löschen
@app.route("/delete/<int:todo_id>")
def delete(todo_id):
    todo = Todo.query.filter_by(id=todo_id).first()
    db.session.delete(todo)
    db.session.commit()
    return redirect(url_for("todo"))

******************************************************************
*  In diesem Abschnitt haben wir unterschiedliche Hilfsmethoden, welche uns bei der Navigierung unterstützen. 


@app.route("/impressum.html")
def impressum():
    return render_template("/impressum.html")

@app.route("/SignIn.html",methods=["GET", "POST"])
def signin():
    return render_template("/SignIn.html")


@app.route("/index.html")
def index():
    return render_template("index.html")


@app.route("/Profil.html")
def profil():
    return render_template("/Profil.html")

****************************************************************
*  Hier haben wir die Main Methode.

if __name__ == "__main__":
    with app.app_context():
        db.create_all()
    app.run(debug=True)



# Html

Die HTML-Dateien befinden sich in einem seperaten Ordner namens templates. Ursprünglich hatten wir
die CSS-Datei ebenfalls in einem seperaten Ordner, doch aufgrund einiger Komplikation haben wir 
uns dazu entschieden die CSS in die HTMl einzubinden. 
Wir haben uns für ein schlichtes Design mit unterschiedlichen Blautöne entschieden. Unter anderem befindet sich auf der Webseite ein "navigation-container", mit dessen Hilfe wir auf die unterschiedlichen Seiten gelangen können. 
Die unterschiedlichen HTML-Seiten ähneln sich vom Aufbau, um ein schönes Gesamtbild zu erzeugen. Sie unterscheiden sich dennoch in gewissen punkten. 

******************************************
* SignIn Webseite
Diese HTML-Datei ist die erste Seite die erscheint, wenn man die Seite öffnet. Hierbei handelt es sich um die Login-Seite. Sie fügt über die Klasse "form-group", in der man seine E-Mail und Passwort eingeben kann.

<!DOCTYPE html>
<html lang="en">
<head>

    <style>
        body {margin: 0;
        }
        a{text-decoration: rgb(9, 15, 98);
        }
        .company-name-container{
        background-color: #142d7a;
        min-height: 200px;
        padding: 30px 20px 5px;
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-nav-container{
        background-color: #8eaaf5;
        padding: 0em 0em 1em;
    }
    .main-content-container{
        background-color: #c0d0f9;
        min-height: 500px;
        list-style: none;
    }
    .field{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        color: #101c41;
    }
    .main-content-container{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-content-container li{
        color: rgb(23, 30, 78);
        font-size: large;
        column-count: 3;
        column-gap: 10em;
        text-align: justify;
        color: #141b1b;
        padding: 1em 2em 2em;
    }
    .main-footer-container{
        background-color: #142d7a;
        min-height: 100px;
        margin-top: 10px;
    }
    .main-nav-container ul{
        margin: 0;
        list-style-type: none;
        font-size: small;
    }
    .main-nav-container a{
        color: inherit;
        text-decoration: none;
    }
    .main-nav>li{
        display:inline-block;
        position: relative;
        color: #1b316a; 
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        font-weight: 600; 
        font-size: 1.7em;
        padding: 0.5em 6em 0;
    }
    .dropdown-nav{
        display: none;
        position: absolute;
        background-color: #a1b3ee;
        width: 100%;
    }
    .dropdown-nav>li{
        padding: 0.5em 1em;   
    }
    .main-nav>li>a:hover{
        color: #161948;
    }
    .dropdown-nav>li:hover{
        background-color: #b6c7fb;
        padding: 0.6em 1em;
        font-size: large;
    }
    .main-nav>li:hover .dropdown-nav{
        display: block;
        font-size: medium;
        padding: 0em 0em;    
    }  
     </style>

    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MeTime</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" 
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

</head>
<body>
    <div class="page-container">
        <header class="company-name-container"><font size="7" color="#D6FFFE">MeTime</font><br>
            <font size="6" color="#D6FFFE">Sign In</font></header>
        <nav class="main-nav-container">
            <ul class="main-nav">
                <li><a href="index.html">Home</a></li>
                <li><a href="ToDos.html">ToDos</a></li>
                <li>Account
                    <ul class="dropdown-nav">
                        <li><a href="SignIn.html">Sign In</a></li>
                        <li><a href="Profil.html">Profil</a></li>
                    </ul>
                </li>
                <li><a href="impressum.html">Impressum</a></li>
            </ul>
        </nav>
        <main class="main-content-container"><div class="row"  >
            <div class="col-sm-6">
                <form method="POST">
                    <div class="form-group">
                      <label for="email">Username : </label>
                      <input type="text" name="uname" class="form-control" id="uname">
                    </div>
                    <div class="form-group">
                      <label for="email">Password : </label>
                      <input type="password" name="passw" class="form-control" id="passw">
                    </div>
                    <button type="submit" class="btn form-control btn-default">Login</button>
                </form>
            </div>
        </div>
    </main>
    </div>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" 
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" 
    crossorigin="anonymous"></script>
        <footer class="main-footer-container"></footer>
    
</body>
</html>

******************************************
* Index Webseite
Nachdem man sich eingeloggt hat, erscheint als nächte seite die Home-Seite. Auf der Home-Seite haben wir unsere Vorteile aufgelistet. 


<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        body {margin: 0;
        }
        a{text-decoration: rgb(9, 15, 98);
        }
        .company-name-container{
        background-color: #142d7a;
        min-height: 200px;
        padding: 30px 20px 5px;
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-nav-container{
        background-color: #8eaaf5;
        padding: 0em 0em 1em;
    }
    .main-content-container{
        background-color: #c0d0f9;
        min-height: 500px;
        list-style: none;
    }
    .field{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        color: #101c41;
    }
    .main-content-container{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-content-container li{
        color: rgb(23, 30, 78);
        font-size: large;
        column-count: 3;
        column-gap: 10em;
        text-align: justify;
        color: #141b1b;
        padding: 1em 2em 2em;
    }
    .main-footer-container{
        background-color: #142d7a;
        min-height: 100px;
        margin-top: 10px;
    }
    .main-nav-container ul{
        margin: 0;
        list-style-type: none;
        font-size: small;
    }
    .main-nav-container a{
        color: inherit;
        text-decoration: none;
    }
    .main-nav>li{
        display:inline-block;
        position: relative;
        color: #1b316a; 
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        font-weight: 600; 
        font-size: 1.7em;
        padding: 0.5em 6em 0;
    }
    .dropdown-nav{
        display: none;
        position: absolute;
        background-color: #a1b3ee;
        width: 100%;
    }
    .dropdown-nav>li{
        padding: 0.5em 1em;   
    }
    .main-nav>li>a:hover{
        color: #161948;
    }
    .dropdown-nav>li:hover{
        background-color: #b6c7fb;
        padding: 0.6em 1em;
        font-size: large;
    }
    .main-nav>li:hover .dropdown-nav{
        display: block;
        font-size: medium;
        padding: 0em 0em;    
    }  
     </style>

    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MeTime</title>
</head>
<body>

        <header class="company-name-container"><font size="7" color="#D6FFFE">MeTime</font><br>
            <font size="6" color="#D6FFFE">Home</font></header>
        <nav class="main-nav-container">
            <ul class="main-nav">
                <li><a href="index.html">Home</a></li>
                <li><a href="ToDos.html">ToDos</a></li>
                <li>Account
                    <ul class="dropdown-nav">
                        <li><a href="SignIn.html">Sign In</a></li>
                        <li><a href="Profil.html">Profil</a></li>
                    </ul></li>
                <li><a href="impressum.html">Impressum</a></li>
            </ul>
        </nav>
        <main class="main-content-container">
            <div align="middle"><font size="5" color="#101c41" face="Times New Roman">
            <br>Mehr als nur das Abhaken der täglichen Aufgaben !<br>
            
            -	Planen Sie Ihren Tag von überall <br><br>
            -	Verpassen Sie nie wieder eine Frist<br><br>
            -	Behalten Sie den Überblick<br></font>
            <br><img src="https://stadt-bremerhaven.de/wp-content/uploads/2018/08/microsoft-to-do-logo.jpg" 
            height="250" width="450" alt="Bild kann nicht geladen werden." border="3" align="center" ></div>
        </main>
        
        <footer class="main-footer-container"></footer>
</body>
</html>

******************************************
* Profil Webseite
Auf der Profil-Seite hat der User die Möglichkeit seine Nutzernamen, E-Mail und Geburtdatum zu unterlegen. Ebenfalls haben wir dort einen Überblick über die Anzahl von ToDos. 


<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        body {margin: 0;
        }
        a{text-decoration: rgb(9, 15, 98);
        }
        .company-name-container{
        background-color: #142d7a;
        min-height: 200px;
        padding: 30px 20px 5px;
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-nav-container{
        background-color: #8eaaf5;
        padding: 0em 0em 1em;
    }
    .main-content-container{
        background-color: #c0d0f9;
        min-height: 500px;
        list-style: none;
    }
    .field{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        color: #101c41;
    }
    .main-content-container{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-content-container li{
        color: rgb(23, 30, 78);
        font-size: large;
        column-count: 3;
        column-gap: 10em;
        text-align: justify;
        color: #141b1b;
        padding: 1em 2em 2em;
    }
    .main-footer-container{
        background-color: #142d7a;
        min-height: 100px;
        margin-top: 10px;
    }
    .main-nav-container ul{
        margin: 0;
        list-style-type: none;
        font-size: small;
    }
    .main-nav-container a{
        color: inherit;
        text-decoration: none;
    }
    .main-nav>li{
        display:inline-block;
        position: relative;
        color: #1b316a; 
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        font-weight: 600; 
        font-size: 1.7em;
        padding: 0.5em 6em 0;
    }
    .dropdown-nav{
        display: none;
        position: absolute;
        background-color: #a1b3ee;
        width: 100%;
    }
    .dropdown-nav>li{
        padding: 0.5em 1em;   
    }
    .main-nav>li>a:hover{
        color: #161948;
    }
    .dropdown-nav>li:hover{
        background-color: #b6c7fb;
        padding: 0.6em 1em;
        font-size: large;
    }
    .main-nav>li:hover .dropdown-nav{
        display: block;
        font-size: medium;
        padding: 0em 0em;    
    }  
     </style>

    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MeTime</title>
</head>
<body>
    <div class="page-container">
        <header class="company-name-container"><font size="7" color="#D6FFFE">MeTime</font><br>
            <font size="6" color="#D6FFFE">Profil</font></header>
        <nav class="main-nav-container">
            <ul class="main-nav">
                <li><a href="index.html">Home</a></li>
                <li><a href="ToDos.html">ToDos</a></li>
                <li>Account
                    <ul class="dropdown-nav">
                        <li><a href="SignIn.html">Sign In</a></li>
                        <li><a href="Profil.html">Profil</a></li>
                    </ul>
                </li>
                <li><a href="impressum.html">Impressum</a></li>
            </ul>
        </nav>
        <main class="main-content-container">
            <div align="middle"><font size="5" color="#101c41" face="Times New Roman">
            <br>Nutzername<br>
            <br>
            	E-mail <br><br>
            	Geburtsdatum<br><br>
            	Anzahl an ToDos<br></font>
               <br> <img src="https://tibatu.com/wp-content/uploads/2020/10/flat-business-man-user-profile-avatar-icon-vector-4333097.jpg"
                 height="200" width="200" alt="Bild kann nicht geladen werden." border="3" align="center"></div></main>
        <footer class="main-footer-container"></footer>
    </div>
</body>
</html>

******************************************
* ToDos Webseite
In dieser HTML-Datei haben wir den ToDo-Bereich. Wir haben die Möglichkeit ToDos einzufügen, zu löschen und zu updaten. 


<!DOCTYPE html>
<html lang="en">
<head>
    <style>
    body {margin: 0;
    }
    a{text-decoration: rgb(9, 15, 98);
    }
    .company-name-container{
    background-color: #142d7a;
    min-height: 200px;
    padding: 30px 20px 5px;
    font-family:'Times New Roman', Times, serif;
    font-size: large;
}
.main-nav-container{
    background-color: #8eaaf5;
    padding: 0em 0em 1em;
}
.main-content-container{
    background-color: #c0d0f9;
    min-height: 500px;
    list-style: none;
}
.field{
    font-family:'Times New Roman', Times, serif;
    font-size: large;
    color: #101c41;
}
.main-content-container{
    font-family:'Times New Roman', Times, serif;
    font-size: large;
}
.main-content-container li{
    color: rgb(23, 30, 78);
    font-size: large;
    column-count: 3;
    column-gap: 10em;
    text-align: justify;
    color: #141b1b;
    padding: 1em 2em 2em;
}
.main-footer-container{
    background-color: #142d7a;
    min-height: 100px;
    margin-top: 10px;
}
.main-nav-container ul{
    margin: 0;
    list-style-type: none;
    font-size: small;
}
.main-nav-container a{
    color: inherit;
    text-decoration: none;
}
.main-nav>li{
    display:inline-block;
    position: relative;
    color: #1b316a; 
    font-family:'Times New Roman', Times, serif;
    font-size: large;
    font-weight: 600; 
    font-size: 1.7em;
    padding: 0.5em 6em 0;
}
.dropdown-nav{
    display: none;
    position: absolute;
    background-color: #a1b3ee;
    width: 100%;
}
.dropdown-nav>li{
    padding: 0.5em 1em;   
}
.main-nav>li>a:hover{
    color: #161948;
}
.dropdown-nav>li:hover{
    background-color: #b6c7fb;
    padding: 0.6em 1em;
    font-size: large;
}
.main-nav>li:hover .dropdown-nav{
    display: block;
    font-size: medium;
    padding: 0em 0em;    
}  
 </style>

    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MeTime</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.css">
    <script src="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.js"></script>
</head>
<body>
    <div class="page-container">
        <header class="company-name-container"><font size="7" color="#D6FFFE">MeTime</font><br>
            <br>
            <font size="6" color="#D6FFFE">ToDos</font></header>
        <nav class="main-nav-container">
            <ul class="main-nav">
                <li><a href="index.html">Home</a></li>
                <li><a href="ToDos.html">ToDos</a></li>
                <li>Account
                    <ul class="dropdown-nav">
                        <li><a href="SignIn.html">Sign In</a></li>
                        <li><a href="Profil.html">Profil</a></li>
                    </ul></li>
                <li><a href="impressum.html">Impressum</a></li>
            </ul>
        </nav>

        <main class="main-content-container">
            <div style="margin-top:  0px;" class="ui container"><br>
                <p align="center"><font size="6" color="#101c41" face="Times New Roman">My ToDos</font><br></p>
                

            <form class="ui form" action="/add" method="post">
                <div class="field">
                    <label>Todo Title</label>
                    <input type="text" name="title" placeholder="Enter Todo..."><br>
                </div>
                <button class="ui purple button" type="submit">Add</button>
            </form>
            <hr>

        {% for todo in todo_list %}
        <div class="ui segment">
            <p class="ui big header">{{todo.id }} | {{ todo.title }}</p>

            {% if todo.complete == False %}
            <span class="ui gray label">Not Complete</span>
            {% else %}
            <span class="ui green label">Completed</span>
            {% endif %}

            <a class="ui blue button" href="/update/{{ todo.id }}">Update</a>
            <a class="ui red button" href="/delete/{{ todo.id }}">Delete</a>
        </div>
        {% endfor %}
   
        </div>
    </main>
        <footer class="main-footer-container"></footer>
    </div>
</body>
</html>

******************************************
* Impressum Webseite

Die HTML-Datei verfügt zusätzlich noch über eine main-content-container, welchen wir mit unseren Daten füllten.

<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        body {margin: 0;
        }
        a{text-decoration: rgb(9, 15, 98);
        }
        .company-name-container{
        background-color: #142d7a;
        min-height: 200px;
        padding: 30px 20px 5px;
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-nav-container{
        background-color: #8eaaf5;
        padding: 0em 0em 1em;
    }
    .main-content-container{
        background-color: #c0d0f9;
        min-height: 500px;
        list-style: none;
    }
    .field{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        color: #101c41;
    }
    .main-content-container{
        font-family:'Times New Roman', Times, serif;
        font-size: large;
    }
    .main-content-container li{
        color: rgb(23, 30, 78);
        font-size: large;
        column-count: 3;
        column-gap: 10em;
        text-align: justify;
        color: #141b1b;
        padding: 1em 2em 2em;
    }
    .main-footer-container{
        background-color: #142d7a;
        min-height: 100px;
        margin-top: 10px;
    }
    .main-nav-container ul{
        margin: 0;
        list-style-type: none;
        font-size: small;
    }
    .main-nav-container a{
        color: inherit;
        text-decoration: none;
    }
    .main-nav>li{
        display:inline-block;
        position: relative;
        color: #1b316a; 
        font-family:'Times New Roman', Times, serif;
        font-size: large;
        font-weight: 600; 
        font-size: 1.7em;
        padding: 0.5em 6em 0;
    }
    .dropdown-nav{
        display: none;
        position: absolute;
        background-color: #a1b3ee;
        width: 100%;
    }
    .dropdown-nav>li{
        padding: 0.5em 1em;   
    }
    .main-nav>li>a:hover{
        color: #161948;
    }
    .dropdown-nav>li:hover{
        background-color: #b6c7fb;
        padding: 0.6em 1em;
        font-size: large;
    }
    .main-nav>li:hover .dropdown-nav{
        display: block;
        font-size: medium;
        padding: 0em 0em;    
    }  
     </style>

    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MeTime</title>
</head>
<body>
    <div class="page-container">
        <header class="company-name-container"><font size="7" color="#D6FFFE">MeTime</font><br>
            <font size="6" color="#D6FFFE">Impressum</font></header>
        <nav class="main-nav-container">
            <ul class="main-nav">
                <li><a href="index.html">Home</a></li>
                <li><a href="ToDos.html">ToDos</a></li>
                <li>Account
                    <ul class="dropdown-nav">
                        <li><a href="SignIn.html">Sign In</a></li>
                        <li><a href="Profil.html">Profil</a></li>
                    </ul>
                </li>
                <li><a href="impressum.html">Impressum</a></li>
            </ul>
        </nav>
        <main class="main-content-container">
            <div style="margin-top: 0px;" class="ui container"><br>
                <p align="center"><font size="6" color="#101c41" face="Times New Roman">MeTime Studio NDC</font><br></p>
        
        <main class="main-content-container"><li><p color="#101c41"> 
            <br>Natasza Aleksandra Kopka<br>
                Haselhorst 23<br>
                Berlin 13583<br>
                Tel.:017680714194<br>
                s_kopka21@stud.hwr-berlin.de<br>
            <br>
            <br>Dilan Kirak<br>
                Spekteweg 51<br>
                Berlin 13583<br>
                Tel.:017630770562<br>
                s_kirak22@stud.hwr-berlin.de<br>
            <br>
            <br>Christina Eduardo Maria Kademba<br>
                Haselhorst 12<br>
                Berlin 13583<br>
                Tel.:017643869820<br>
                s_kademba21@stud.hwr-berlin.de<br>
        </p></li>
        </main>
                
    </div>
 </main>
        <footer class="main-footer-container"></footer>
    </div>
</body>
</html>




