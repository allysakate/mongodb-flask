# mongodb-flask

from pymongo import MongoClient

# connection
client = MongoClient('mongodb://localhost:27017/')
# data base name : 'test'
db = client['test']

db.posts.drop()

import datetime

post = [   
    
        {"author": "Duke",
        "title" : "PyMongo 101",
        "tags" : ["MongoDB", "PyMongo", "Tutorial"],
        "date" : datetime.datetime.utcnow()
        },
        {"author": "Duke 5",
        "title" : "PyMongo 101 - 5",
        "tags" : ["MongoDB 5", "PyMongo 101 - A5", "Tutorial 5"],
        "date" : datetime.datetime.utcnow()
        },
        { "author": "Duke II",
          "title" : "PyMongo II 101",
          "tags" : ["MongoDB II", "PyMongo II", "Tutorial II"],
          "date" : datetime.datetime.utcnow() 
        },
        { "author": "Duke III",
          "title" : "PyMongo III 101",
          "tags" : ["MongoDB III", "PyMongo III", "Tutorial III"],
          "date" : datetime.datetime.utcnow() 
        }]

posts = db.posts
post_id = posts.insert(post)

print ('post_id', post_id)
print ('collection name:' ,db.collection_names())

new_post = [
            {"author": "Duke 6",
              "title" : "PyMongo 101-A6",
              "tags" : ["MongoDB 6", "PyMongo 6", "Tutorial 6"],
              "date" : datetime.datetime(2015, 11, 28, 12, 13)},
            {"author": "Adja",
              "title": "MongoDB 101-A7",
              "note": "Schema free MongoDB",
              "date": datetime.datetime(2015, 11, 29, 11, 42)}
            ]

db.posts.insert(new_post)

count_authors = db.posts.count()
print ("author's no:", count_authors)

for doc in db.posts.find():
   print (doc)

print ('Row where author is Adja')
for doc1 in db.posts.find({"author": "Adja"}):
   print (doc1)

----------------
# -*- coding: utf-8 -*-

from flask import Flask, jsonify, url_for, redirect, request
from flask_pymongo import PyMongo
from flask_restful import Api, Resource

app = Flask(__name__)
app.config["MONGO_DBNAME"] = "test"
mongo = PyMongo(app, config_prefix='MONGO')
APP_URL = "http://127.0.0.1:5000"

# PostsList
# shows a list of all authors, and lets you POST to add new tasks
class PostList(Resource):
    def get(self):
        docs = []
        for doc in mongo.db.posts.find():
            doc.pop('_id') 
            docs.append(doc)
        return jsonify(docs)

    def post(self):
        args = parser.parse_args()
        post_id= int(max(data.keys()).lstrip('author')) + 1
        post_id = 'post%i' % post_id
        data[post_id] = {'author': args['author']}
        return data[post_id], 201

##
## Actually setup the Api resource routing here
##
api = Api(app)
api.add_resource(PostList, '/author')


if __name__ == '__main__':
    app.run(debug=True)
