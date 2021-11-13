<template>
  <h3>BusinessUnit web api client</h3>
  <div id="app">
    <span>
      <input type="text" v-model="ApiUrl" placeholder="Api Url" style="font-size:16px"/>
      <input
        style="font-size:16px"
        type="button"
        value="Refresh"
        @click="RefreshButtonAction()"
      />
    </span>
    <div style="padding-top: 20px" />
    <table id="tableID" style="width:100%" border="1">
      <tr>
        <td>ID</td>
        <td>Name</td>
        <td>FullName</td>
        <td>Type</td>
        <td>Inn</td>
        <td>Kpp</td>
        <td>Delete</td>
      </tr>
      <tr v-for="item in items" :key="item.id">
        <td><label v-text="item.id"/></td>
        <td><label v-text="item.name"/></td>
        <td><label v-text="item.fullName"/></td>
        <td><label v-text="item.typeString"/></td>
        <td><label v-text="item.inn"/></td>
        <td><label v-text="item.kpp"/></td>
        <td>
          <input
            style='width:100%; height:100%;'
            type="button"
            value="&#215;"
            @click="DeleteButtonAction(item.id)"
          />
        </td>
      </tr>
    </table>
    <div style="padding-top: 20px" />
    <label style="padding-right: 10px">Add new BusinessUnit</label>
    <div> 
      <input type="text" v-model="NameInput" placeholder="Name" />
      <input type="text" v-model="InnInput" placeholder="Inn" />
      <input type="text" v-model="KppInput" placeholder="Kpp" />
      <label style="padding-right: 10px">
        <input
          type="checkbox"
          v-bind:checked="IsLegalSelected"
          @change="TypeSelectAction"
          />Legal
      </label>
      <label style="padding-right: 15px">
        <input
          type="checkbox"
          v-bind:checked="!IsLegalSelected"
          @change="TypeSelectAction"
        />Individual
        </label>
      <input type="button" value="Add" @click="AddButtonAction"/>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  mounted() {
    this.RefreshButtonAction();
  },
  methods: {
    RefreshButtonAction: function () {
      this.items = [];
      axios
        .get(this.ApiUrl + "/GetAllBusinessUnits", {
          headers: { Accept: "text/json" },
        })
        .then((response) => this.items = response.data)
        .catch((error) => alert(error.response.data));
    },
    DeleteButtonAction: function (id) {
      axios
        .delete(this.ApiUrl + "/DeleteBusinessUnit", {
          params: { id: id },
        })
        .then((response) => this.RefreshButtonAction())
        .catch((error) => alert(error.response.data));
    },
    TypeSelectAction: function () {
      this.IsLegalSelected = !this.IsLegalSelected;
    },
    AddButtonAction: function () {
      var data = new FormData();
      data.append('Name', this.NameInput);
      data.append('Inn', this.InnInput);
      data.append('Kpp', this.KppInput);
      data.append('Type', this.IsLegalSelected ? "LEGAL" : "INDIVIDUAL");
      axios
        .post(this.ApiUrl + "/AddBusinessUnit", data)
        .then((response) => this.RefreshButtonAction())
        .catch((error) => alert(error.response.data));
    },
  },
  data() {
    return {
      ApiUrl: "http://localhost:21706",
      NameInput: "",
      InnInput: "",
      KppInput: "",
      IsLegalSelected: true,
      items: []
    };
  },
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
