<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>HTML Firebase CRUD</title>
	<script src=
"https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js">
	</script>

	<link rel="stylesheet" href=
"https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css"
		integrity=
"sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2"
		crossorigin="anonymous">
</head>
<style>
h1{
  font-size: 35px;
}
li{
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 8px;
  cursor: pointer;
  list-style: none;
  color: rgb(0, 0, 0);
  counter-increment: number;
}
li:before{
  content: counter(number) ". ";
}
p:last-child{
  color: #a00;
}
#user-detail{
  background: #eee;
  color: #555;
  font-weight: bold;
  display: inline-block;
  padding: 5px 155px 0 10px;
  font-size: 15px;
  margin-top: 25px;
}
li button{
  margin: 0px 10px;
}

#addForm,#editForm{
  margin-bottom: 20px;
}
input{
  margin: 5px 0px;
  padding: 8px;
}
</style>

<body>

<div class="jumbotron text-center">
  <h1>Firebase CRUD</h1>

</div>


<div class="container" style="margin-top: 50px;
	width: 50%; height:auto;">
<div class="row">
	<div class="col-sm-6">
	  <div class="card">
		<div class="card-body">
		  <h5 class="card-title">Insert data</h5>

		
		  <form action="#" id="addForm">
			<input type="text" class="user-input" data-key="name" placeholder="type name here" id="text1"><br>

			<input type="text" class="user-input" data-key="age" placeholder="type data here" id="text2"><br>
			<button type="submit" class="btn btn-success">Insert data</button>
		 </form>
		</div>
	  </div>
	</div>
	<div class="col-sm-6">
	  <div class="card">
		<div class="card-body">
		  <h5 class="card-title">Edit data</h5>
		 
		  <form action="#" id="editForm">
			<input type="hidden" id="edit-userid">
			<input type="text" class="edit-user-input" data-key="name" placeholder="type name here" ><br>

			<input type="text" class="edit-user-input" data-key="age" placeholder="type data here"><br>
			<button type="submit" id="saveUser" class="btn btn-warning">Edit data</button>
		 </form>
	 
		</div>
	  </div>
	</div>
  </div>
  


	
	<div id="wrapper">
		<h1>Collection Data</h1>
    <p id="demo"></p>

		<div id="container"></div>
		<div id="user-detail" ></div>
	</div>  
</div>  
</div>

  

<script src=
"https://www.gstatic.com/firebasejs/3.7.4/firebase.js">
	</script>
	
<script>



		 // Your web app's Firebase configuration
		 var firebaseConfig = {
		  apiKey: "AIzaSyDIPVDNnd7311xX2v-zuh6B4OzmAjHwinw",
  authDomain: "dottextdb.firebaseapp.com",
  databaseURL: "https://dottextdb-default-rtdb.firebaseio.com",
  projectId: "dottextdb",
  storageBucket: "dottextdb.appspot.com",
  messagingSenderId: "909623431795",
  appId: "1:909623431795:web:614fed070712f5309a5006",
  measurementId: "G-KVDD7GS9PR"
  };
  // Initialize Firebase
  firebase.initializeApp(firebaseConfig);


const dbRef = firebase.database().ref();
const userRef = dbRef.child('users');
const container = document.getElementById("container");

userRef.on("child_added", function(snap){
  let user = snap.val();   // single obj stored in "user"
  
  let $li = document.createElement("li");
  let $delBtn = document.createElement("button");
  $delBtn.className = "btn btn-danger";
  let $editBtn = document.createElement("button");
  $editBtn.className = "btn btn-warning";

  $delBtn.innerHTML = "Delete";
  $delBtn.setAttribute("child-key", snap.key);
  $delBtn.addEventListener('click', deleteUser);
  
  $editBtn.innerHTML = "Edit";
  $editBtn.setAttribute("child-key", snap.key);
  $editBtn.addEventListener("click", editUser);
  
  
  $li.innerHTML = user.name;    // user.name == "bingo" or "hey"
  $li.setAttribute("child-key", snap.key);
  $li.addEventListener("click", userClicked);
  
  container.append($li);
  $li.append($delBtn);
  $li.append($editBtn);
});

function userClicked(e){
  e.preventDefault();
  
  var userID = e.target.getAttribute("child-key");
  const userRef = dbRef.child(`users/${userID}`);
  
  const userDetail = document.getElementById("user-detail");
  userDetail.innerHTML = "";   // empty old data
  
  userRef.on("child_added", function(snap){
    var $p = document.createElement("p");
   
    $p.innerHTML = `${snap.key}: ${snap.val()}`;
    userDetail.append($p);
  })   
}


// add-Users in DataBase

var form = document.getElementById("addForm");
form.addEventListener("submit", addUser);

function addUser(e){
  e.preventDefault();
  
  const  userRef = dbRef.child('users');
  var userInput = document.getElementsByClassName('user-input');
  
  let newUser = {};   // empty obj to store data in it before pushing it to Database
  
  for(var i = 0; i < userInput.length; i++){
    // we run loop to get all input keys and value.
    let key = userInput[i].getAttribute('data-key');
    let value = userInput[i].value;
    
    newUser[key] = value;
  }
  userRef.push(newUser);
}



// Delete User

function deleteUser(e){
  e.preventDefault();
  e.stopPropagation();
  
  var userID = e.target.getAttribute('child-key');
  const userRef = dbRef.child(`users/${userID}`);
  userRef.remove();

  container.textContent = '';
  load();
}


function load(){
  
  const dbRef = firebase.database().ref();
const userRef = dbRef.child('users');
const container = document.getElementById("container");

userRef.on("child_added", function(snap){
  let user = snap.val();   // single obj stored in "user"
  
  let $li = document.createElement("li");
  let $delBtn = document.createElement("button");
  $delBtn.className = "btn btn-danger";
  let $editBtn = document.createElement("button");
  $editBtn.className = "btn btn-warning";


  $delBtn.innerHTML = "Delete";
  $delBtn.setAttribute("child-key", snap.key);
  $delBtn.addEventListener('click', deleteUser);
  
  $editBtn.innerHTML = "Edit";
  $editBtn.setAttribute("child-key", snap.key);
  $editBtn.addEventListener("click", editUser);
  
  
  $li.innerHTML = user.name;    // user.name == "bingo" or "hey"
  $li.setAttribute("child-key", snap.key);
  $li.addEventListener("click", userClicked);
  
  container.append($li);
  $li.append($delBtn);
  $li.append($editBtn);
});

}
//Edit User


function editUser(e){

	console.log(e.target.getAttribute('child-key'));



  e.preventDefault();
  e.stopPropagation();
  
  document.getElementById('edit-userid').value = e.target.getAttribute("child-key"); 
  var userRef = dbRef.child("users/" + e.target.getAttribute("child-key"));
  
  var editInput = document.getElementsByClassName("edit-user-input");
  
  userRef.on("value", function(snap){
    for(var i = 0; i< editInput.length; i++){
      var key  = editInput[i].getAttribute("data-key");
      editInput[i].value = snap.val()[key];
    }
  })
  const saveBtn = document.getElementById("saveUser");
  saveBtn.addEventListener("click",saveUser);
}


// saveData to Db onClick to saveBtn

function saveUser(e){
  e.preventDefault();
  e.stopPropagation();
  
  const userID = document.getElementById("edit-userid").value;
  const userRef = dbRef.child("users/"+userID);
  const editInput  = document.querySelectorAll(".edit-user-input");
  var editedObject = {};

  for(var i = 0; i < editInput.length; i++){
    let key = editInput[i].getAttribute("data-key"); // name,email,age
    let value = editInput[i].value;     // rango, rango@gmail.., 22 etc
    editedObject[key] = value;
  }
  userRef.update(editedObject);  
}


		</script>
		
</body>

</html>
