<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Lab 9 - To Do List (DOM)</title>
<style>
  body { font-family: Arial; background:#f2f2f2; padding:20px }
  #box { background:white; width:400px; margin:auto; padding:20px; border-radius:8px; }
  input { width:75%; padding:8px; }
  button { padding:7px 10px; margin-left:5px }
  ul{padding:0}
  li{
    list-style:none;
    background:#eee;
    margin:6px 0;
    padding:8px;
    border-radius:5px;
    display:flex;
    justify-content:space-between;
    align-items:center;
  }
  .done { text-decoration:line-through; color:gray }
  .editInput{ width:70%; padding:5px; }
</style>
</head>
<body>

<div id="box">
  <h2>To-Do List</h2>

  <input id="taskInput" type="text" placeholder="Add task..." />
  <button onclick="addTask()">Add</button>

  <ul id="list"></ul>
</div>

<script>
  let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
  let taskInput = document.getElementById("taskInput");
  let list = document.getElementById("list");

  // ------- ENTER to ADD -------
  taskInput.addEventListener("keypress", e => {
    if(e.key === "Enter") addTask();
  });

  // ------- ADD -------
  function addTask(){
    let text = taskInput.value.trim();
    if(text === "") return;
    tasks.push({text, done:false});
    taskInput.value = "";
    save();
    render();
  }

  // ------- EDIT -------
  function editTask(index){
    let li = list.children[index];
    let oldSpan = li.querySelector("span");

    let input = document.createElement("input");
    input.className = "editInput";
    input.value = tasks[index].text;

    input.addEventListener("keypress", e=>{
      if(e.key === "Enter") finishEdit(index, input.value);
    });

    input.addEventListener("blur", ()=>{
      finishEdit(index, input.value);
    });

    oldSpan.replaceWith(input);
    input.focus();
  }

  function finishEdit(i, newText){
    if(newText.trim() === "") return;
    tasks[i].text = newText;
    save();
    render();
  }

  // ------- DELETE -------
  function deleteTask(i){
    tasks.splice(i,1);
    save();
    render();
  }

  // ------- TOGGLE COMPLETE -------
  function toggleDone(i){
    tasks[i].done = !tasks[i].done;
    save();
    render();
  }

  // ------- SAVE -------
  function save(){
    localStorage.setItem("tasks", JSON.stringify(tasks));
  }

  // ------- RENDER -------
  function render(){
    list.innerHTML = "";
    tasks.forEach((t,i)=>{
      let li = document.createElement("li");

      let span = document.createElement("span");
      span.innerText = t.text;
      if(t.done) span.classList.add("done");
      span.onclick = ()=> toggleDone(i);

      let editBtn = document.createElement("button");
      editBtn.innerText = "Edit";
      editBtn.onclick = ()=> editTask(i);

      let delBtn = document.createElement("button");
      delBtn.innerText = "X";
      delBtn.onclick = ()=> deleteTask(i);

      li.append(span, editBtn, delBtn);
      list.append(li);
    });
  }

  // Load on page start
  render();
</script>

</body>
</html>
