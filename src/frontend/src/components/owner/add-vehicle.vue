<template>
  <div v-if="loggedIn">
    <div class="container-fluid">
      <div class="row flex-nowrap">
        <div
          class="bg-dark col-auto col-md-4 col-lg-3 min-vh-100 d-flex flex-column justify-content-between"
        >
          <div class="bg-dark p-2">
            <a
              href=""
              class="d-flex text-decoration-none mt-1 align-items-center text-white"
            >
              <span class="fs-4 d-none d-sm-inline">Owners Dashboard</span>
            </a>
            <ul class="nav nav-pills flex-column mt-4">
              <li class="nav-item py-2 py-sm-0">
                <a href="/owner/dashboard" class="nav-link text-white"
                  ><i class="bx bxs-dashboard"></i
                  ><span class="fs-6 d-none ms-3 d-sm-inline"
                    >Dashboard</span
                  ></a
                >
              </li>
              <li class="nav-item py-2 py-sm-0">
                <a href="#" class="nav-link text-white"
                  ><i class="bx bxs-user-circle"></i
                  ><span class="fs-6 d-none ms-3 d-sm-inline">Profile</span></a
                >
              </li>
              <li class="nav-item py-2 py-sm-0">
                <a href="/owner/vehicle" class="nav-link active"
                  ><i class="bx bx-list-ul"></i
                  ><span class="fs-6 d-none ms-3 d-sm-inline">Vehicles</span></a
                >
              </li>
              <li class="nav-item py-2 py-sm-0">
                <a href="/owner/bookings" class="nav-link text-white"
                  ><i class="bx bx-book-content"></i
                  ><span class="fs-6 d-none ms-3 d-sm-inline">Bookings</span></a
                >
              </li>
            </ul>
          </div>
          <div class="dropdown open p-3">
            <button
              class="btn border-none dropdown-toggle text-white"
              type="button"
              id="triggerId"
              data-bs-toggle="dropdown"
              aria-expanded="false"
            >
              <i class="bx bxs-user-circle"></i
              ><span class="ms-2">{{ owner.fullname }}</span>
            </button>
            <div class="dropdown-menu" aria-labelledby="triggerId">
              <button class="dropdown-item" href="#" @click="logout">
                Logout
              </button>
            </div>
          </div>
        </div>

        <!-- CONTENT  -->

        <div class="p-3">
          <h2>
            Add vehicle |
            <a href="/owner/vehicle" class="btn btn-secondary"
              ><i class="bx bx-arrow-back"></i> Back</a
            >
          </h2>
          <hr />
          <form @submit.prevent="registerVehicle" style="width: 75%;">
            <label for="">Manufacturer: </label>
            <input
              type="text"
              v-model="vehicle.manufacturer"
              class="form-control"
            />
            <label for="">Model:</label><br />
            <input type="text" v-model="vehicle.model" class="form-control" />
            <label for="">Daily Fair:</label>
            <input
              type="number"
              v-model="vehicle.dailyfare"
              class="form-control"
            />
            <label for="">From Date:</label>
            <input
              type="date"
              v-model="vehicle.fromdate"
              class="form-control"
            />
            <label for="">To Date:</label>
            <input type="date" v-model="vehicle.todate" class="form-control" />
            <label for="">Category:</label>
            <select v-model="vehicle.category" class="form-control">
              <option value="">---Select Category---</option>
              <option v-for="category in categories" :key="category.category_id" :value="category.category_name">{{ category.category_name }}</option>
            </select>
            <label for="">Available Location:</label>
            <select v-model="vehicle.availablelocation" class="form-control">
              <option value="">---Select Location---</option>
              <option v-for="location in locations" :key="location.location_id" :value="location.location">{{ location.location }}</option>
            </select>
            <label for="">Vehicle Image:</label>
            <input
              type="file"
              @change="handleFileUpload"
              class="form-control"
            />

            <input
              type="hidden"
              name="owners_id"
              class="form-control"
              :value="owner.owners_id"
            />
            <label for="">Description</label>
            <textarea
              cols="30"
              rows="10"
              v-model="vehicle.description"
              class="form-control"
            ></textarea>
            <!-- <input type="text" v-model="vehicle.description" class="form-control" /> -->
            <br />
            <input type="submit" value="Register" class="btn btn-primary" />
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.nav-pills li a:hover {
  background-color: blue;
}
</style>

<script>
import axios from "axios";
export default {
  name: "addVehicle",
  data() {
    return {
      owner: {},
      loggedIn: false,
      vehicle: {
        manufacturer: "",
        model: "",
        dailyfare: "",
        fromdate: "",
        todate: "",
        availablelocation: "",
        category: "",
        description: "",
        owners_id: "",
        carimg: null,
      },
      locations: [],
      categories: [],
    };
  },
  created() {
    this.fetchUserData();
    this.fetchLocations();
    this.fetchCategories();
  },
  methods: {
    handleFileUpload(event) {
      this.vehicle.carimg = event.target.files[0];
    },
    registerVehicle() {
      const formData = new FormData();

      const vehicleData = {
        manufacturer: this.vehicle.manufacturer,
        model: this.vehicle.model,
        dailyfare: this.vehicle.dailyfare,
        fromdate: this.vehicle.fromdate,
        todate: this.vehicle.todate,
        availablelocation: this.vehicle.availablelocation,
        category: this.vehicle.category,
        description: this.vehicle.description,
        owners_id: this.owner.owners_id,
      };

      formData.append(
        "vehicle",
        new Blob([JSON.stringify(vehicleData)], { type: "application/json" })
      );

      formData.append("image", this.vehicle.carimg);

      console.log("Image", this.vehicle.carimg);

      // formData.append("image", this.vehicle.carimg);
      // formData.append("vehicle", JSON.stringify(vehicleData));

      console.log("Image", this.vehicle.carimg);
      console.log("Owner: ", this.owner.owners_id);
      console.log("Vehicle: ", this.vehicle.manufacturer);
      console.log("Model: ", this.vehicle.model);
      console.log("Dailyfare: ", this.vehicle.dailyfare);
      console.log("Fromdate: ", this.vehicle.fromdate);
      console.log("Todate: ", this.vehicle.todate);
      console.log("Availablelocation: ", this.vehicle.availablelocation);
      console.log("Description: ", this.vehicle.description);

      console.log("working");

      console.log("Formdata:", formData);
      
      axios
        .post("/api/vehicle/add", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(() => {
          alert("Vehicle registered"),
            (window.location.href = "/owner/vehicle");
        })
        .catch((error) => console.error("Error registering vehicle:", error));
    },
    fetchUserData() {
      axios
        .get("/api/owner/me")
        .then((response) => {
          this.owner = response.data;
          this.loggedIn = true;
          console.log("Owner data:", this.owner);
        })
        .catch((error) => {
          console.error(error);
          this.$router.push("/owner/login");
        });
    },
    fetchLocations() {
      axios
        .get("/api/location/all")
        .then((response) => {
          this.locations = response.data; // Assuming the response is an array of locations
        })
        .catch((error) => {
          console.error("Error fetching locations:", error);
        });
    },

    fetchCategories() {
      axios
        .get("/api/category/all")
        .then((response) => {
          this.categories = response.data; // Assuming the response is an array of categories
        })
        .catch((error) => {
          console.error("Error fetching categories:", error);
        });
    },

    logout() {
      console.log("logout working");
      axios
        .get("/api/owner/logout")
        .then(() => {
          window.location.href = "/owner/login";
        })
        .catch((error) => {
          console.error(error);
        });
    },
  },
};
</script>