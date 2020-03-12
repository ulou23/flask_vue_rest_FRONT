<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>SEND your URLs</h1>
<br>
        <b-form @save="onSubmit" class="w-100" >
          <b-input id="inline-form-mail" type="email" placeholder="Your Mail" ></b-input>
          <br>
          <b-button variant="primary" type="save">Save</b-button>
        </b-form>
        <br>
          <b-form @submit="onSubmit" @reset="onReset" class="w-100">
            <b-form-group>
              <b-form-input id="form-url-input"
                            type="url"
                            v-model="addUrlForm.urlinput"
                            required
                            placeholder="Enter url">
              </b-form-input>
            </b-form-group>

            <b-button-group>
              <b-button type="submit" variant="primary">Submit</b-button>
              <b-button type="reset" variant="danger">Reset</b-button>
            </b-button-group>
          </b-form>
        <br><br>
        <table class="table table-hover">
          <thead>
          <tr>
            <th scope="col">YourUrl</th>
            <th></th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(url, index) in urls" :key="index">
            <td>{{ url.urlinput }}</td>
            <td>
              <div class="btn-group" role="group">
                <button type="button" class="btn btn-info btn-sm">Update</button>
                <button type="button" class="btn btn-danger btn-sm">Delete</button>
              </div>
            </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      urls: [],
      addUrlForm: {
        urlinput: '',
      },
    };
  },
  methods: {
    getUrls() {
      const path = 'http://localhost:5000/urls';
      axios.get(path)
        .then((res) => {
          this.urls = res.data.urls;
        })
        .catch((error) => {
          // eslint-disable-next-line
            console.error(error);
        });
    },
    addUrl(payload) {
      const path = 'http://localhost:5000/urls';
      axios.post(path, payload)
        .then(() => {
          this.getUrls();
        })
        .catch((error) => {
          // eslint-disable-next-line
            console.log(error);
          this.getUrls();
        });
    },
    initForm() {
      this.addUrlForm.urlinput = '';
    },
    onSubmit(evt) {
      evt.preventDefault();
      const payload = {
        urlinput: this.addUrlForm.urlinput,
      };
      this.addUrl(payload);
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.initForm();
    },
  },
  created() {
    this.getUrls();
  },
};
</script>
