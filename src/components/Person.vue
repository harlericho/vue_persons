<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-3">
        <div class="card" id="card">
          <div class="card-body" id="card-body">
            <h5 class="card-title">Form</h5>
            <form
              method="POST"
              v-on:submit.prevent="onSubmit()"
              enctype="multipart/form-data"
            >
              <input type="hidden" v-model="person.id" />
              <div class="mb-3">
                <label for="dni">Dni</label>
                <input
                  type="text"
                  v-model="person.dni"
                  class="form-control form-control-sm text-white bg-dark"
                  ref="dni"
                  minlength="10"
                  maxlength="10"
                  required
                />
              </div>
              <div class="mb-3">
                <label for="names">Names</label>
                <input
                  type="text"
                  v-model="person.names"
                  class="form-control form-control-sm text-white bg-dark"
                  ref="names"
                  required
                />
              </div>
              <div class="mb-3">
                <label for="email">Email</label>
                <input
                  type="email"
                  v-model="person.email"
                  class="form-control form-control-sm text-white bg-dark"
                  ref="email"
                  required
                />
              </div>
              <div class="mb-3">
                <div class="container">
                  <img v-if="viewPhoto" :src="viewPhoto" alt="" width="50" />
                </div>
              </div>
              <div class="mb-3">
                <label for="view">Photo</label>
                <div class="container">
                  <input
                    type="file"
                    class="form-control form-control-sm text-white bg-dark"
                    v-on:change="onChangeView()"
                    ref="photo"
                    accept="image/*"
                  />
                </div>
              </div>
              <button type="submit" class="btn btn-success btn-sm" id="margin">Add</button>
              <button
                type="button"
                v-on:click="onReset()"
                class="btn btn-primary btn-sm"
              >
                New
              </button>
            </form>
          </div>
        </div>
      </div>
      <div class="col-sm-9">
        <div class="card" id="card">
          <div class="card-body" id="card-body">
            <h5 class="card-title">List</h5>
            <div class="table table-responsive">
              <table class="table table-hover table-inverse text-white">
                <thead class="thead-inverse">
                  <tr>
                    <th>#</th>
                    <th>Dni</th>
                    <th>Names</th>
                    <th>Email</th>
                    <th>Photo</th>
                    <th>Status</th>
                    <th colspan="2">Options</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(list, i) in listData" :key="list.id">
                    <td scope="row">{{ i + 1 }}</td>
                    <td>{{ list.dni }}</td>
                    <td>{{ list.names }}</td>
                    <td>{{ list.email }}</td>
                    <td>
                      <img :src="src + list.photo" alt="photo" width="50" />
                    </td>
                    <td v-if="list.status === 'A'">
                      <span class="badge bg-success">Active</span>
                    </td>
                    <td v-else>
                      <span class="badge bg-warning">Inactive</span>
                    </td>
                    <td>
                      <button
                        class="btn btn-success btn-sm"
                        v-on:click="getData(list)"
                        id="margin"
                      >
                        Edit
                      </button>
                      <button
                        class="btn btn-danger btn-sm"
                        v-on:click="deleteData(list)"
                      >
                        Delete
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Swal from "sweetalert2";
// url of the api
const URL = "http://localhost:7000/?option";
// url of image public
const URL_IMAGE = "http://localhost:7000/";
export default {
  data() {
    return {
      person: {
        id: 0,
        dni: "",
        names: "",
        email: "",
      },
      src: URL_IMAGE + "public/uploads/",
      listData: [],
      viewPhoto: "",
    };
  },
  mounted() {
    // load function when the component is mounted
    this.getListData();
    // auto focus of the input main when the component is mounted
    this.$refs.dni.focus();
  },
  methods: {
    // function view photo
    onChangeView() {
      // Read the file from the input
      let file = this.$refs.photo.files[0];
      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onloadend = () => {
        this.viewPhoto = reader.result;
      };
    },
    // function get list data
    getListData() {
      axios
        .get(URL + "=GET")
        .then((res) => {
          this.listData = res.data;
        })
        .catch((err) => {
          console.error(err);
        });
    },
    // function on submit of the form
    onSubmit() {
      let formData = new FormData();
      formData.append("id", this.person.id);
      formData.append("dni", this.person.dni);
      formData.append("names", this.person.names);
      formData.append("email", this.person.email);
      formData.append("file", this.$refs.photo.files[0]);
      if (this.person.id === 0) {
        this.addData(formData);
      } else {
        this.updateData(formData);
      }
    },
    // function add data
    addData(formData) {
      axios
        .post(URL + "=POST", formData)
        .then((res) => {
          if (res.data === "DNI is already taken") {
            Swal.fire({
              title: "Error",
              text: res.data,
              icon: "error",
              confirmButtonText: "Ok",
            });
            this.$refs.dni.focus();
          } else if (res.data === "EMAIL is already taken") {
            Swal.fire({
              title: "Error",
              text: res.data,
              icon: "error",
              confirmButtonText: "Ok",
            });
            this.$refs.email.focus();
          } else if (res.data === true) {
            Swal.fire({
              title: "Success",
              text: "Data has been added",
              icon: "success",
              confirmButtonText: "Ok",
            });
            this.getListData();
            this.onReset();
          }
        })
        .catch((err) => {
          console.error(err);
        });
    },
    // function update data
    updateData(formData) {
      // let formData = new FormData();
      // formData.append("id", this.person.id);
      // formData.append("dni", this.person.dni);
      // formData.append("names", this.person.names);
      // formData.append("email", this.person.email);
      // formData.append("file", this.$refs.photo.files[0]);
      axios
        .post(URL + "=PUT", formData)
        .then((res) => {
          if (res.data === "DNI is already taken") {
            Swal.fire({
              title: "Error",
              text: res.data,
              icon: "error",
              confirmButtonText: "Ok",
            });
            this.$refs.dni.focus();
          } else if (res.data === "EMAIL is already taken") {
            Swal.fire({
              title: "Error",
              text: res.data,
              icon: "error",
              confirmButtonText: "Ok",
            });
            this.$refs.email.focus();
          } else if (res.data === true) {
            Swal.fire({
              title: "Success",
              text: "Data has been updated",
              icon: "success",
              confirmButtonText: "Ok",
            });
            this.getListData();
            this.onReset();
          }
        })
        .catch((err) => {
          console.error(err);
        });
    },
    // function get data by id
    getData(list) {
      this.person.id = list.id;
      this.person.dni = list.dni;
      this.person.names = list.names;
      this.person.email = list.email;
      this.viewPhoto = this.src + list.photo;
      this.$refs.dni.focus();
    },
    // function delete data by id
    deleteData(list) {
      var idForm = new FormData();
      idForm.append("id", list.id);
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!",
      }).then((result) => {
        if (result.value) {
          if (list.status !== "I") {
            axios
              .post(URL + "=DELETE", idForm)
              .then((res) => {
                if (res.data === true) {
                  Swal.fire({
                    title: "Success",
                    text: "Data has been deleted",
                    icon: "success",
                    confirmButtonText: "Ok",
                  });
                  this.getListData();
                }
              })
              .catch((err) => {
                console.error(err);
              });
          } else {
            Swal.fire({
              title: "Error",
              text: "Registration is already inactive",
              icon: "error",
              confirmButtonText: "Ok",
            });
          }
        }
      });
    },
    // function on reset of the form
    onReset() {
      this.person.id = 0;
      this.viewPhoto = "";
      this.person.dni = "";
      this.person.names = "";
      this.person.email = "";
      this.$refs.photo.value = "";
      this.$refs.dni.focus();
    },
  },
};
</script>
