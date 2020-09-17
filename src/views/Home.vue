<template>
  <div class="row">
    <div class="col-lg-12">
      <router-link class="btn btn-outline-dark" to="/reservation">Add Reservation</router-link>
      <table class="table table-bordered">
        <thead>
          <tr>
            <th>id</th>
            <th>Room</th>
            <th>Check In</th>
            <th>Check Out</th>
            <th>Days</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in info_reservation" :key="item.id_reservation">
            <td>{{item.id_reservation}}</td>
            <td>{{item.name}}</td>
            <td>{{item.checkin}}</td>
            <td>{{item.checkout}}</td>
            <td>{{item.days}}</td>
            <td class="text-center">              
              <router-link class="btn btn-warning btn-sm btn-size" :to="{ name: 'reservation', params: { id: item.id_reservation }}">Edit</router-link>
              <button
                type="button"
                class="btn btn-danger btn-sm btn-size"
                @click="destroy(item.id_reservation)"
              >Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { http } from "../services";
import swal from "sweetalert";

export default {
  name: "Home",
  data() {
    return {
      info_reservation: [],
      loading: false,
    };
  },
  mounted() {
    console.info("App this router:", this.$router);
    console.info("App currentRoute:", this.$router.currentRoute);
    console.info("App currentRoute:", this.$router.currentRoute.name);

    this.load();
  },
  methods: {
    load() {
      http
        .get("http://localhost:8000/api/room/availability")
        .then((response) => {
          console.log(response.data);
          if (response.data.status == false) {
            console.error(response.data);
          }
          this.loading = true;
          this.info_reservation = response.data.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    destroy(dataId) {
      console.log("dataId", dataId);
      swal({
        title: "Are you sure?",
        text: "Once deleted, you will not be able to recover!",
        icon: "warning",
        buttons: true,
        dangerMode: true,
      }).then((willDelete) => {
        if (willDelete) {
          swal("record has been deleted!", {
            icon: "success",
          });

          http
            .delete("http://localhost:8000/api/room/reservation", {
              data: { id: dataId },
            })
            .then((response) => {
              console.log(response.data);
              if (response.data.status == true) {
                this.load();
              }
            })
            .catch((error) => {
              console.log(error);
            });
        } else {
          //swal("Your imaginary file is safe!");
        }
      });
    },
  },
};
</script>

<style lang="scss">
.btn-size {
  width: 90px;
  height: 30px;
}
</style>
