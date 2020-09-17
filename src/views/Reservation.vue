<template>
  <div class="row">
    <div class="col-lg-12">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Create Reservation</h5>
          <form v-on:submit.prevent="validForm ? save(): update()">
            <div class="form-group">
              <label for="exampleFormControlSelect2">Type of Room</label>
              <select
                class="form-control"
                id="exampleFormControlSelect2"
                required
                v-model="form.type"
              >
                <option value>[ choose option ]</option>
                <option
                  v-for="item in select_info_room"
                  :key="item.id_room"
                  :value="item.id_room"
                >{{item.name}}</option>
              </select>
            </div>
            <div class="form-group">
              <label for="daterange">Date</label>
              <input
                type="text"
                name="daterange"
                class="form-control"
                id="daterange"
                placeholder="choose date"
                required
              />
            </div>
            <div class="row">
              <div class="col-md-6">
                <button type="submit" class="btn btn-primary btn-lg btn-block">Send</button>
              </div>
              <div class="col-md-6">
                <router-link class="btn btn-secondary btn-lg btn-block" to="/">Cancel</router-link>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>



<script>
import * as $ from "jquery";
import "daterangepicker/daterangepicker.js";
import "daterangepicker/daterangepicker.css";

import { http } from "../services";
import swal from "sweetalert";

export default {
  name: "Reservation",
  data() {
    return {
      loading: false,
      select_info_room: [],
      form: {
        type: "",
        checkin: "",
        checkout: "",
      },
      validForm: true,
    };
  },
  mounted() {
    console.info("App this router:", this.$router);
    console.info("App currentRoute:", this.$router.currentRoute);
    console.info("App currentRoute:", this.$router.currentRoute.name);
    console.info("App paramas:", this.$route.params);

    if (Object.entries(this.$route.params).length > 0) {
      this.validForm = false;
      this.filter(this.$route.params.id);
    }

    /**
     * PULL INFO ROOM
     */

    http
      .get("http://localhost:8000/api/room/list", this.form)
      .then((response) => {
        console.log(response.data);
        if (response.data.status == false) {
          console.error(response.data);
        }
        this.select_info_room = response.data.data;
      })
      .catch((error) => {
        console.log(error);
      });

    /**
     * CREATE DATA RANGER PICKER
     */

    $('input[name="daterange"]').daterangepicker(
      {
        opens: "left",
        autoUpdateInput: false,
      },
      (start, end) => {
        this.form.checkin = start.format("YYYY-MM-DD");
        this.form.checkout = end.format("YYYY-MM-DD");
        console.log(
          "A new date selection was made: " +
            start.format("YYYY-MM-DD") +
            " to " +
            end.format("YYYY-MM-DD")
        );
      }
    );

    $('input[name="daterange"]').on("apply.daterangepicker", function  (ev, picker) {
      $(this).val(
        picker.startDate.format("MM/DD/YYYY") +
          " - " +
          picker.endDate.format("MM/DD/YYYY")
      );
      console.log(ev, picker);
      this.form.checkin = picker.startDate.format("YYYY-MM-DD");
      this.form.checkout = picker.startDate.format("YYYY-MM-DD");
    });

    $('input[name="daterange"]').on("cancel.daterangepicker", (ev, picker) => {
      console.log(ev, picker);
      $(this).val("");
    });

    // end datapicker
  },
  methods: {
    filter(dataId) {
      http
        .get("http://localhost:8000/api/room/reservation_filter", {
          params: { id: dataId },
        })
        .then((response) => {
          console.log(response.data);
          if (response.data.status == true) {
            console.log(response.data);
            this.form.type = response.data.data[0].id_room;

            $('input[name="daterange"]').daterangepicker(
              {
                opens: "left",
              },
              (start, end) => {
                this.form.checkin = start.format("YYYY-MM-DD");
                this.form.checkout = end.format("YYYY-MM-DD");
                console.log(
                  "A new date selection was made: " +
                    start.format("YYYY-MM-DD") +
                    " to " +
                    end.format("YYYY-MM-DD")
                );
              }
            );

            $('input[name="daterange"]')
              .data("daterangepicker")
              .setStartDate(response.data.data[0].checkin);
            $('input[name="daterange"]')
              .data("daterangepicker")
              .setEndDate(response.data.data[0].checkout);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    save() {
      this.loading = true;
      console.log("send request", this.form);

      http
        .post("http://localhost:8000/api/room/reservation", this.form)
        .then((response) => {
          console.log(response.data);
          if (response.data.status == true) {
            swal({
              title: "Data sent",
              text: "Your information has been received satisfactorily",
              icon: "success",
            });

            this.$router.push("/");
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    update() {
      this.loading = true;
      console.log("send request", this.form);
      this.form.id = this.$route.params.id;

      http
        .put("http://localhost:8000/api/room/reservation", this.form)
        .then((response) => {
          console.log(response.data);
          if (response.data.status == true) {
            swal({
              title: "Data sent",
              text: "Your information has been received satisfactorily",
              icon: "success",
            });

            this.$router.push("/");
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>

<style lang="scss">
</style>
