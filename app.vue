<template>
  <div>
    <h1>{{ name }}</h1>
    <h2>{{ Title1 }}</h2>
    <ul>
      <li v-for="Project in Projects" :key="Project.name">
        <h2>{{ Project.name }}</h2>
        <p>{{ Project.description }}</p>
        <p><b>ID:</b>{{ Project._id }}</p>
      </li>
    </ul>
    <h2>{{ Title2 }}</h2>
    <ul>
      <li v-for="Blog in Blogs" :key="Blog.Title">
        <h2>{{ Blog.title }}</h2>
        <p>{{ Blog.content }}</p>
        <p>{{ Blog.date }}</p>
      </li>
    </ul>
  </div>
  <div>
    <h1>Create a New Project</h1>
    <form @submit.prevent="createProject">
      <table>
        <tr>
          <td><label for="name">Project Name:</label></td>
          <td>
            <input type="text" v-model="newProject.name" id="name" required />
          </td>
        </tr>
        <tr>
          <td><label for="description">Project Description:</label></td>
          <td>
            <textarea
              id="description"
              v-model="newProject.description"
              required
            ></textarea>
          </td>
        </tr>
        <tbody>
          <td></td>
          <td><button type="submit">Create Project</button></td>
        </tbody>
      </table>
    </form>
  </div>
  <div>
    <h1>Update a Project</h1>
    <form @submit.prevent="changeProject">
      <table>
        <tr>
          <td><label for="id">Project ID(Identify by Name):</label></td>
          <td>
            <select v-model="updateProject.id" id="ProjecttID" required>
              <option
                v-for="Project in Projects"
                :key="Project._id"
                :value="Project._id"
              >
                {{ Project.name }}
              </option>
            </select>
          </td>
        </tr>
        <tr>
          <td><label for="name">Project Name:</label></td>
          <td>
            <input
              type="text"
              v-model="updateProject.name"
              id="name"
              required
            />
          </td>
        </tr>
        <tr>
          <td><label for="description">Project Description:</label></td>
          <td>
            <textarea
              id="description"
              v-model="updateProject.description"
              required
            ></textarea>
          </td>
        </tr>
        <tr>
          <td></td>
          <td><button type="submit">Update Project</button></td>
        </tr>
      </table>
    </form>
  </div>
  <div>
    <h1>Delete a Project</h1>
    <form @submit.prevent="removeProject">
      <table>
        <tr>
          <td><label for="id">Project ID(Identify by Name):</label></td>
          <td>
            <select v-model="deleteProject.id" id="ProjecttID" required>
              <option
                v-for="Project in Projects"
                :key="Project._id"
                :value="Project._id"
              >
                {{ Project.name }}
              </option>
            </select>
          </td>
        </tr>
        <tr>
          <td></td>
          <td><button type="submit">Delete Project</button></td>
        </tr>
      </table>
    </form>
  </div>
</template>

<script setup>
const name = "Dinith Edirisinghe";
const Title1 = "Projects";

const {
  data: Projects,
  pending1,
  error1,
} = useFetch("http://localhost:5000/projects");
const Title2 = "Blogs";
const {
  data: Blogs,
  pending2,
  error2,
} = useFetch("http://localhost:5000/blogs");

//Create a new project
const newProject = ref({
  name: "",
  description: "",
});

//Function to create a new project
const createProject = async () => {
  console.log(newProject.value);

  try {
    const response = await fetch("http://localhost:5000/projects", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(newProject.value),
    });

    if (!response.ok) {
      throw new Error("Failed to create a new project");
    }

    //Update the backend with new project
    const result = await response.json();
    Projects.value.push(result); //Add new project to the list

    //clear the form
    newProject.value.name = "";
    newProject.value.description = "";
  } catch (error) {
    console.error(error);
  }
};

//Update a project
const updateProject = ref({
  id: "",
  name: "",
  description: "",
});

//Function to Update the project using project id
const changeProject = async () => {
  console.log(updateProject.value);

  try {
    const response = await fetch(
      `http://localhost:5000/projects/${updateProject.value.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(updateProject.value),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to update the project");
    }

    //Update the backend with updated project
    const result = await response.json();

    //replace the element within project list by checking the _id
    const index = Projects.value.findIndex(
      (project) => project._id === updateProject.value.id
    );
    if (index !== -1) {
      Projects.value[index] = result;
    }

    //clear the form
    updateProject.value.id = "";
    updateProject.value.name = "";
    updateProject.value.description = "";
  } catch (error) {
    console.error(error);
  }
};

//Delete a project
const deleteProject = ref({
  id: "",
});

//Function to delete the project using project id
const removeProject = async () => {
  console.log(deleteProject.value);

  try {
    const response = await fetch(
      `http://localhost:5000/projects/${deleteProject.value.id}`,
      {
        method: "DELETE",
      }
    );

    if (!response.ok) {
      throw new Error("Failed to delete the project");
    }

    //update the backend with deleted project
    const result = await response.json();
    //Remove the project from the list
    const index = Projects.value.findIndex(
      (project) => project._id === deleteProject.value.id
    );
    if (index !== -1) {
      Projects.value.splice(index, 1);
    }

    //clear the form
    deleteProject.value.id = "";
  } catch (error) {
    console.error(error);
  }
};
</script>

<style scoped>
h1 {
  color: blue;
}
h2 {
  color: green;
}
</style>
