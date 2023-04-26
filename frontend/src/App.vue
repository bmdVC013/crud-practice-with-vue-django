<template>
  <div id="app">
    <form @submit.prevent="submitForm">
      <div class="form-group row">
        <div class="col-3">
          <input
            v-model="student.name"
            type="text"
            class="form-control"
            placeholder="Name"
          >
        </div>
        <div class="col-3">
          <input
            v-model="student.course"
            type="text"
            class="form-control"
            placeholder="Course"
          >
        </div>
        <div class="col-3">
          <input
            v-model="student.rating"
            type="text"
            class="form-control"
            placeholder="Rating"
          >
        </div>

        <div class="col-3 text-start">
          <button
            type="submit"
            class="btn btn-success"
          >
            Submit
          </button>
          <button
            class="btn btn-secondary ms-2"
            type="button"
            @click="$data.student = initializeStudent()"
          >
            Clear
          </button>
        </div>
      </div>
    </form>

    <table class="table mt-3">
      <thead>
        <th>Name</th>
        <th>Course</th>
        <th>Rating</th>
        <th class="w-10">Action</th>
      </thead>
      <tbody>
        <tr
          v-for="student in students"
          :key="student.id"
          @dblclick="$data.student = JSON.parse(JSON.stringify(student))"
        >
          <td>{{ student.name }}</td>
          <td>{{ student.course }}</td>
          <td>{{ student.rating }}</td>
          <td>
            <button
              class="btn btn-outline-danger btn-sm mx-1"
              @click="$data.student = JSON.parse(JSON.stringify(student))"
            >
              Edit
            </button>
            <button
              class="btn btn-outline-danger btn-sm mx-1"
              @click="deleteStudent(student)"
            >
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
const initializeStudent = () => ({
  name: "",
  course: "",
  rating: "",
});

export default {
  name: "App",

  data() {
    return {
      students: [],
      student: initializeStudent(),
    };
  },

  async created() {
    await this.getStudents();
  },

  methods: {
    submitForm() {
      if (this.student.id === undefined) {
        this.createStudent();
        return;
      }

      this.editStudent();
    },

    async getStudents() {
      const response = await fetch("http://localhost:8000/api/students");
      this.students = await response.json();
    },

    async createStudent() {
      const response = await fetch("http://localhost:8000/api/students/", {
        method: "post",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(this.student),
      });
      const newStudent = await response.json();

      this.students.push(newStudent);
    },

    async editStudent() {
      await fetch(`http://localhost:8000/api/students/${this.student.id}/`, {
        method: "put",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(this.student),
      });

      this.student = initializeStudent();
      this.getStudents();
    },

    async deleteStudent(student) {
      await fetch(`http://localhost:8000/api/students/${student.id}/`, {
        method: "delete",
        headers: {
          "Content-Type": "application/json",
        },
      });

      this.getStudents();
    },

    initializeStudent,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
