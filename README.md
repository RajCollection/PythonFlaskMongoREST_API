## PythonFlaskMongoREST_API

#### install flask
    pip install flask
   
#### set flask    (run in command prompt)
    set FLASK_APP=app
    
#### run flask    (run in command prompt)
    flask run
    
#### if u want to change port number, by default it is 5000    (run in command prompt)
    set FLASK_RUN_PORT=8000
    
#### if u want to change custom host
    flask run --host=0.0.0.0

#### API Code (app.py)

    from flask import Flask
    from pymongo import MongoClient
    from flask import request
    import json
    from bson.json_util import dumps
    from bson.objectid import ObjectId

    client = MongoClient('localhost:27017')
    db = client.myDB

    app = Flask(__name__)

    @app.route("/")
    def welcome():
        return "Welcome to Flask APIs"

    # Add User
    @app.route("/user", methods = ['POST'])
    def add_user():
        try:
            data = json.loads(request.data)
            name = data['name']
            email = data['email']
            password = data['password']
            phone = data['phone']

            if name and email:
                status = db.Users.insert_one({
                    "name": name,
                    "email": email,
                    "password": password,
                    "phone": phone
                })
            return dumps({'message' : 'SUCCESS'})
        except Exception:
            return dumps({'error': str(Exception)})


    # Get All Users
    @app.route("/users", methods=['GET'])
    def get_all_users():
        try:
            users = db.Users.find()
            return dumps(users)
        except Exception:
            return dumps({'error': 'Server Error'})


    # Get User by ID
    @app.route("/user/<user_id>", methods=['GET'])
    def get_user(user_id):
        try:
            users = db.Users.find({'_id':ObjectId(user_id)})
            return dumps(users)
        except Exception:
            return dumps({'error': 'Server Error'})


    # Update User
    @app.route("/user/<user_id>", methods = ['PUT'])
    def update_user(user_id):
        try:
            data = json.loads(request.data)

            status = db.Users.update({'_id':ObjectId(user_id)}, data, upsert=False)
            return dumps({'message' : 'SUCCESS'})
        except Exception:
            return dumps({'error': 'Server Error'})


    # Delete User
    @app.route("/user/<user_id>", methods=['DELETE'])
    def remove_user(user_id):
        """
           Function to remove the user.
           """
        try:
            # Delete the user
            delete_user = db.Users.remove({"_id": ObjectId(user_id)})
            return dumps({'message' : 'SUCCESS'})

        except:
            # Error while trying to delete the resource
            # Add message for debugging purpose
            return dumps({'message': 'Server Error'})



