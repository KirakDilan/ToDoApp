# ToDoApp
from flask import Flask, render_template, request, redirect, url_for, flash, session, logging
from flask_sqlalchemy import SQLAlchemy // Diese Zeilen muss ganz am Anfang des files stehen

/*************************************************************
* Vorname , Nachname:   Dilan Kirak                          * 
 * Studiengang: Wirtschaftsinformatik                        * 
 * Martikelnummer:    77211925676                            * 
 * Ort: HWR- Hochschule für Wirtschaft und Recht             *
 * @author Dilan	            	                         *
 *                                                           *
* Vorname , Nachname:   Natasza Alexandra Kopka              * 
 * Studiengang: Wirtschaftsinformatik                        * 
 * Martikelnummer:    77211880478                            * 
 * Ort: HWR- Hochschule für Wirtschaft und Recht             *
 * @author Natazca	            	                         *
 *                                                           *
* Vorname , Nachname:   Christina Eduardo Maria Kademba      * 
 * Studiengang: Wirtschaftsinformatik                        * 
 * Martikelnummer:     7721188143                            * 
 * Ort: HWR- Hochschule für Wirtschaft und Recht             *
 * @author Christina	            	                     *
 ************************************************************* */

/*******************************************************************************************************************************************
* THEMA
Hierbei handelt es sich um eine Webseite einer ToDo-Liste. Beim Öffnen der Webseite sieht man als erstes die Loginseite. Der Benutzer kann sich hier einloggen und somit ein eigenes Profil erstellen. Auf dieser Profilseite hat der Nutzer die Möglichkeit einen Nutzernamen, E-Mail & Geburtsdatum zu hinterlegen. Ebenfalls hat man ein Überblick über die Anzahl von ToDos. Nach der Anmeldung wird man auf die Home-Seite weitergeleitet. In diesem Bereiche haben wir unsere Vorteile aufgelistet. 
Auf der ToDo-Seite hat man die Möglichkeit ToDo's einzufügen, upzudaten und zu löschen. Der Nutzer trägt seine ToDo in das Textfeld ein und drückt auf "Add", dann wird diese ToDo in die Liste aufgenommen. Sobald diese Aufgabe erledigt ist, drückt der Nutzer auf "Ubdate" und die Aufgabe wird als "completed" makiert. Durch das drücken von "Delete" wird die Aufgabe aus die Liste gestrichen. 
Zu guter letzt haben wir noch einen Impressum-Bereich mit unseren Angaben. 

****************************************************************************************************************************************** */

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
app.config['SECRET_KEY'] = 'secretkey'

db = SQLAlchemy(app)
//....

/**
*   ****************************************************************
* In der Prozedur haben wir eine Klasse, welche dem User eine Login-Basis zu Verfügung stellt. 
* 
* ****************************************************************** */

#Login
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), nullable=False, unique=True)#kann nicht leer sein
    password = db.Column(db.String(80), nullable=False)#kann nicht leer sein



#Erste Seite die sich öffnet 
@app.route("/")
def home():
    return render_template("SignIn.html")
//koordiniert Sie beim Starten der Webseite auf die SignIn.html

//koordiniert Sie auf die Index.html

#Login Methode
@app.route("/SignIn",methods=["GET", "POST"])
def login():
    if request.method == "POST":
        uname = request.form["uname"]
        passw = request.form["passw"]
        
        login = User.query.filter_by(username=uname, password=passw).first()
        if login is not None:
            return redirect(url_for("index"))
        return render_template("index.html", login=login)

#Login Seite
@app.route("/SignIn.html",methods=["GET", "POST"])
def signin():
    return render_template("/SignIn.html")


#Startseite
@app.route("/index.html")
def index():
    return render_template("index.html")
    //koordiniert Sie auf die Index.html

#Profil Seite
@app.route("/Profil.html")
def profil():
    return render_template("/Profil.html")
    //koordiniert Sie auf die Profil.html


/**
*   ****************************************************************
* In der Prozedur haben wir eine Klasse, welche dem User eine ToDo-Basis zu Verfügung 
* stellt. Hier haben wir die Id als Integer, den Title als String und die "Complete" als Boolean.  
* 
* ****************************************************************** */

#Todos
class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100))
    complete = db.Column(db.Boolean)


@app.route("/ToDos.html")
def todo():
    todo_list = Todo.query.all()
    return render_template("ToDos.html", todo_list=todo_list)
    //koordiniert Sie auf die ToDos.html


/**
*   ****************************************************************
* In der Methode haben wir als Default-Wert "POST". Hier haben wir den Add-Bereich, welches dem User 
* die Möglichkeit gibt ein neues ToDo einzufügen, zu updateten und zu löschen. 
* 
* ****************************************************************** */


#Neue todos hinzufügen
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

#Impressum Seite
@app.route("/impressum.html")
def impressum():
    return render_template("/impressum.html")
    //koordiniert Sie auf die impressum.html

/**
*   ****************************************************************
*  Hier haben wir die Main Methode.
*    
* ****************************************************************** */

if __name__ == "__main__":
    with app.app_context():
        db.create_all()
    app.run(debug=True)



# Html

/****************************************************************************************************
* Die HTML-Dateien befinden sich in einem seperaten Ordner namens templates. Ursprünglich hatten wir
* die CSS-Datei ebenfalls in einem seperaten Ordner, doch aufgrund einiger Komplikation haben wir 
* uns dazu entschieden die CSS in HTMl einzubinden. 
* Wir haben uns für ein schlichtes Design mit unterschiedlichen Blautöne entschieden. 
* 
****************************************************************************************************/






