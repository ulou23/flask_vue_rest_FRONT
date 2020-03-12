<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>SEND your URLs</h1>
<br>
        <b-form @save="onSubmit" class="w-100" >  <!-- MAIL send events zrobic -->
          <b-input id="inline-form-mail" type="email" placeholder="Your Mail" ></b-input>
          <br>
          <b-button variant="primary" type="submit">Send</b-button>
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
                <button type="button"
                        class="btn btn-info btn-sm"
                        v-b-modal.url-update-modal
                        @click="editUrl(url)">Update</button>
                <button type="button"
                        class="btn btn-danger btn-sm"
                        @click="onDeleteUrl(url)">Delete</button>
              </div>
            </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
    <b-modal ref="editUrlModal"
             id="url-update-modal"
             title="Update"
             hide-footer>
      <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
        <b-form-group id="form-urlinput-edit-group"
                      label="Url:"
                      label-for="form-url-edit-input">
          <b-form-input id="form-url-edit-input"
                        type="text"
                        v-model="editForm.urlinput"
                        required
                        placeholder="Update path">
          </b-form-input>
        </b-form-group>
        <b-button-group>
          <b-button type="submit" variant="primary">Update</b-button>
          <b-button type="reset" variant="danger">Cancel</b-button>
        </b-button-group>
      </b-form>
    </b-modal>
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
      editForm: {
        id: '',
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
      this.editForm.id = '';
      this.editForm.urlinput = '';
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
    editUrl(url) {
      this.editForm = url;
    },
    onSubmitUpdate(evt) {
      evt.preventDefault();
      this.$refs.editUrlModal.hide();
      const payload = {
        urlinput: this.editForm.urlinput,
      };
      this.updateUrl(payload, this.editForm.id);
    },
    updateUrl(payload, urlID) {
      const path = `http://localhost:5000/urls/${urlID}`;
      axios.put(path, payload)
        .then(() => {
          this.getUrls();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getUrls();
        });
    },
    onResetUpdate(evt) {
      evt.preventDefault();
      this.$refs.editUrlModal.hide();
      this.initForm();
      this.getUrls();
    },
    removeUrl(urlID) {
      const path = `http://localhost:5000/urls/${urlID}`;
      axios.delete(path)
        .then(() => {
          this.getUrls();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getUrls();
        });
    },
    onDeleteUrl(urlinput) {
      this.removeUrl(urlinput.id);
    },
  },
  created() {
    this.getUrls();
  },
};
</script>
