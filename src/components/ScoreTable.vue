<template>
  <div>
    <div class="form-user">

      <form class="userform" @submit.prevent="searchUser">
        <label for="userneme">username</label>
        <input v-model="username" placeholder="Enter Username" name="username" />
        <button type="submit">search</button>
      </form>

    </div>
    
    <div  v-if="error" class="error">{{ error }}</div>

    <div v-if="events.data">
      <table class="table">
        <thead>
          <tr>
            <th>Type</th>
            <th>Repository</th>
            <th>Date</th>
            <th>Points</th>
          </tr>
        </thead>
        <tbody v-for="event in events.data" :key="event.id">
          <tr>
            <td data-label="Type">{{ event.type }}</td>
            <td data-label="Repository">{{ event.repository }}</td>
            <td data-label="Date"> Created On {{ transferDate(event.date) }}</td>
            <td data-label="Points">{{ event.points }}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td></td>
            <td></td>
            <td></td>

            <th>{{ events.score }}</th>
          </tr>
        </tfoot>
      </table>


    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "ScoreTable",
  data() {
    return {
      username: "",
      error: "",
      events: {},
      score: 0
    };
  },
  methods: {
    transferDate(date) {
      var options = { day: 'numeric', month: 'long', year: 'numeric' };
      var newDate = new Date(date);
      return newDate.toLocaleDateString("en-US", options);

    },
    async searchUser() {
      this.error = "";
      this.events = {};

      if (!this.username) {
        this.error = "the username is required";
      } else {
        let formData = new FormData();
        formData.append("username", this.username);
        let url = "http://127.0.0.1:8000/api/getscore";
        await axios.post(url, formData).then(response => {
          if (response.status == 200) {
            this.events = response.data;
          } else {
            this.error = response.data.message;
          }
        });
      }
    }
  }
};
</script>

<style>
.table {
  width: 100%;
  border-collapse: collapse;
}

.table td,
.table th {
  padding: 12px 15px;
  border: 1px solid #ddd;
  text-align: center;
  font-size: 16px;
}

.table th {
  background-color: darkblue;
  color: #ffffff;
}

.table tbody tr:nth-child(even) {
  background-color: #f5f5f5;
}

/*responsive*/
@media (max-width: 500px) {
  .table thead {
    display: none;
  }

  .table,
  .table tbody,
  .table tr,
  .table td {
    display: block;
    width: 100%;
  }

  .table tr {
    margin-bottom: 15px;
  }



  .table td {
    text-align: right;
    padding-left: 50%;
    text-align: right;
    position: relative;
  }

  .table td::before {
    content: attr(data-label);
    position: absolute;
    left: 0;
    width: 50%;
    padding-left: 15px;
    font-size: 15px;
    font-weight: bold;
    text-align: left;
  }
}

button {
  height: 30px;
  border-radius: 20px;
  border: solid gray 1px;
  background: rgb(78, 173, 173);
  padding: 1px 10px;
}

input {
  display: inline-block;
  margin: 10px 7px 0 0;
  height: 30px;
  border-radius: 20px;
  background: #f5f5f5;
  padding: 1px 10px;
  border: solid gray 1px;
}

.form-user {
  width: 100vw;
  padding: 20px 30px
}

label {
  display: block;
}

.userform {
  margin-left: 40%;
}

tfoot td {
  border: none !important;
}
.error {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  background-color: #f2dede;
  border-color: #ebccd1;
  color: #a94442;
}
</style>
