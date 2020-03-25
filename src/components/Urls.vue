<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>SEND your RSS URLs</h1>
<br>
        <b-form @submit="Send" class="w-100" >  <!-- MAIL send events zrobic -->
          <b-input id="inline-form-mail" v-model="addMailForm.mail" type="email"
                   placeholder="Your Mail"></b-input>
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
                            placeholder="Enter RSS url">
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
    <br>
<b-container>
      <b-card-group deck class="col-sm-10">
        <b-card v-for="(url, index) in urls" :key="index"
        bg-variant="info" text-variant="white"
                img-alt="Image"
                img-top>

          <b-card img-src="https://picsum.photos/600/300/?image=25"></b-card>
          <b-card-title> {{ url.urlinput }}</b-card-title>
          <b-card-text> {{ url.urlinput }}</b-card-text>
          <b-button target="_new" :href="url.urlinput" variant="primary">
            Read more</b-button>

        </b-card>

      </b-card-group>
</b-container>
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
    <VueRssParser :feedUrl="feedUrl" :name="name" :limit="limit"/>
</div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      urls: [],
      // allItems: [],
      addUrlForm: {
        urlinput: '',
      },
      editForm: {
        id: '',
        urlinput: '',
      },
      addMailForm: {
        mail: '',
      },
    };
  },
  methods: {
    getUrls() {
      const path = 'https://sendurlsback.azurewebsites.net/urls';
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
      const path = 'https://sendurlsback.azurewebsites.net/urls';
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
    addMail(payload) {
      const path = 'https://sendurlsback.azurewebsites.net/send';
      axios.post(path, payload)
        .then((res) => {
          // eslint-disable-next-line no-param-reassign
          res.payload = this.mail;
        }).catch((error) => {
          // eslint-disable-next-line
          console.error(error);
        });
    },
    initForm() {
      this.addUrlForm.urlinput = '';
      this.editForm.id = '';
      this.editForm.urlinput = '';
      this.addMailForm = '';
    },
    Send(evt) {
      evt.preventDefault();
      const payload = {
        mail: this.addMailForm.mail,
      };
      this.addMail(payload);
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
      const path = `https://sendurlsback.azurewebsites.net/urls/${urlID}`;
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
      const path = `https://sendurlsback.azurewebsites.net/urls/${urlID}`;// 'http://localhost:5000/urls
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
    // loaditems(url) {
    //   const Parser = require('rss-parser');
    //   const parser = new Parser();
    //   (async () => {
    //     const feed = parser.parseURL(url);
    //   });
    // },
  },
  //  loadFeed(feed) {
  //    fetch(encodeURIComponent(feed.urlinput))
  //      .then((res) => res.json())
  //      .then((res) => {
  //        res.feed.items.forEach((item => {
  //          item.title = feed.title;
  //          this.allItems.push(item);
  //        });
  //      });
  //  },
  //  restoreFeeds() {
  //    const feeds = localStorage.getItem('urls');
  //    this.feeds = JSON.parse(feeds);
  //    this.feeds.forEach((feed) => {
  //      this.loadFeed(feed);
  //    });
  //  },
  created() {
    this.getUrls();
    // this.restoreFeeds();
  },
};
</script>
