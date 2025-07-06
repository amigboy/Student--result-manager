# Student--result-manager
<!DOCTYPE html>
<html>
<head>
  <title>Student Result Manager</title>
  <style>
    body { font-family: Arial; padding: 20px; }
    input, button { margin: 5px; padding: 5px; }
    #output { margin-top: 20px; white-space: pre-wrap; }
  </style>
</head>
<body>

  <h2>Student Result Management System</h2>

  <input id="studentId" type="number" placeholder="ID" />
  <input id="studentName" type="text" placeholder="Name" />
  <input id="studentScore" type="number" placeholder="Score" />
  <button onclick="addStudentFromInput()">Add Student</button>
  <br/>

  <button onclick="displayAllStudents()">Show All Students</button>
  <button onclick="displayRankedStudents()">Show Ranked Students</button>
  <input id="searchName" type="text" placeholder="Search Name" />
  <button onclick="searchStudentByName()">Search</button>
  <br/>
  <button onclick="exportToCSV()">Export to CSV</button>

  <div id="output"></div>

  <script>
    class Student {
      constructor(id, name, score) {
        this.id = id;
        this.name = name;
        this.score = score;
      }
    }

    class TreeNode {
      constructor(student) {
        this.student = student;
        this.left = null;
        this.right = null;
      }
    }

    class StudentBST {
      constructor() {
        this.root = null;
      }

      insert(student) {
        const _insert = (node, student) => {
          if (!node) return new TreeNode(student);
          if (student.score < node.student.score) {
            node.left = _insert(node.left, student);
          } else {
            node.right = _insert(node.right, student);
          }
          return node;
        };
        this.root = _insert(this.root, student);
      }

      inorder() {
        const result = [];
        const _inorder = (node) => {
          if (node) {
            _inorder(node.right);
            result.push(node.student);
            _inorder(node.left);
          }
        };
        _inorder(this.root);
        return result;
      }

      search(name) {
        const _search = (node) => {
          if (!node) return null;
          if (node.student.name.toLowerCase() === name.toLowerCase()) return node.student;
          return _search(node.left) || _search(node.right);
        };
        return _search(this.root);
      }
    }

    const studentArray = [];
    const bst = new StudentBST();

    function addStudentFromInput() {
      const id = parseInt(document.getElementById("studentId").value);
      const name = document.getElementById("studentName").value;
      const score = parseInt(document.getElementById("studentScore").value);

      if (!name || isNaN(id) || isNaN(score)) {
        show("Please fill all fields correctly.");
        return;
      }

      const student = new Student(id, name, score);
      studentArray.push(student);
      bst.insert(student);

      show(`Student ${name} added.`);
      document.getElementById("studentId").value = '';
      document.getElementById("studentName").value = '';
      document.getElementById("studentScore").value = '';
    }

    function displayAllStudents() {
      let output = "\nAll Students:\n";
      studentArray.forEach(s => {
        output += `ID: ${s.id}, Name: ${s.name}, Score: ${s.score}\n`;
      });
      show(output);
    }

    function displayRankedStudents() {
      const ranked = bst.inorder();
      let output = "\nRanked Students:\n";
      ranked.forEach((s, i) => {
        output += `${i + 1}. ${s.name} (Score: ${s.score})\n`;
      });
      show(output);
    }

    function searchStudentByName() {
      const name = document.getElementById("searchName").value;
      const result = bst.search(name);
      if (result) {
        show(`Found: ${result.name} (ID: ${result.id}, Score: ${result.score})`);
      } else {
        show("Student not found.");
      }
    }

    function exportToCSV() {
      let csv = "ID,Name,Score\n";
      studentArray.forEach(s => {
        csv += `${s.id},${s.name},${s.score}\n`;
      });

      const blob = new Blob([csv], { type: "text/csv" });
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url;
      a.download = "students.csv";
      a.click();
    }

    function show(message) {
      document.getElementById("output").textContent = message;
    }
  </script>

</body>
</html>
