<template>
  <div id="view-employee">
    <ul class="collection with-header">
      <li class="collection-header">
        {{name}}
      </li>
      <li class="collection-item">EmployeeID#
        {{employee_id}}
      </li>
      <li class="collection-item">
        {{department}}
      </li>
      <li class="collection-item">
        {{position}}
      </li>
      </ul>
      <router-link to="/" class="btn grey">Back</router-link>
      <button @click="deleteEmployee" class="btn red">Delete</button>
 
      <div class="fixed-action-btn">
    <router-link v-bind:to="{name:'edit-employee',params:{employee_id:employee_id}}" 
    class="btn-floating btn-large red">
    <i class="fa fa-pencil"></i>
    </router-link>
    </div>
   
  </div>
</template>

<script>
import db from "./firebaseInit";
export default {
  name: "view-employee",
  data() {
    return {
      employee_id: null,
      name: null,
      department: null,
      position: null
    };
  },
  beforeRouteEnter(to, from, next) {
    db
      .collection("employees")
      .where("employee_id", "==", to.params.employee_id)
      .get()
      .then(querySnapshot => {
        querySnapshot.forEach(doc => {
          next(vm => {
            vm.employee_id = doc.data().employee_id;
            vm.name = doc.data().name;
            vm.position = doc.data().position;
            vm.department = doc.data().department;
          });
        });
      });
  },
  watch: {
    $route: "fetchData"
  },
  methods: {
    fetchData() {
      db
        .collection("employees")
        .where("employee_id", "==", this.$route.params.employee_id)
        .get()
        .then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.employee_id = doc.data().employee_id;
            this.name = doc.data().name;
            this.department = doc.data().department;
            this.position = doc.dat().position;
          });
        });
    },
    deleteEmployee() {
      if (confirm("Are you sure?")) {
        db
          .collection("employees")
          .where("employee_id", "==", this.$route.params.employee_id)
          .get()
          .then(querySnapshot => {
            querySnapshot.forEach(doc => {
              doc.ref.delete();
              this.$router.push("/");
            });
          });
      }
    }
  }
};
</script>
