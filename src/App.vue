<template>
  <div id="app">
    <section class="section">
      <div class="container">
        <div v-if="!this.accessToken" class="notification is-warning">
          Enter your client ID below.
        </div>
        <div class="columns">
          <div v-if="!this.accessToken" class="column">
            <nav class="panel">
              <div class="panel-block">
                <p class="control has-icons-left">
                  <input v-model="clientId" class="input" type="text" placeholder="Client ID here">
                  <span class="icon is-small is-left">
                    <i class="fa fa-key"></i>
                  </span>
                </p>
              </div>
              <div class="panel-block">
                <button v-on:click="getToken()" class="button is-link is-outlined is-fullwidth">
                  Click to Authenticate
                </button>
              </div>
            </nav>
          </div>
          <div class="column">
            <nav class="panel">
              <p class="panel-heading">
                Search Artists
              </p>
              <div class="panel-block">
                <p class="control has-icons-left">
                  <input v-model="artist" @keyup.enter="getArtist" class="input" type="text" placeholder="Search for your faves">
                </p>
              </div>

              <div class="track-listing">
              <a v-for="track in tracks" class="panel-block is-active">
                  {{ track.name }}
                  {{ track.artists[0].name }}
                  <audio
                    :src="track.preview_url"
                    controls>
                    Your browser does not support the <code>audio</code> element.
                  </audio>
              </a>
            </div>
            </nav>
          </div>
        </div>  
      </div>
    </section>
  </div>
</template>

<script>
import Axios from 'axios'

export default {
  name: 'app',
  data () {
    // Todo: ClientID hardcoded in source code is not great practice, usually you would want to include config/passwords like this in '.env' files and exclude from version control.
    // But this will work for purposes of getting this project working
    return {
      clientId: '008b5c2685ad40bead402e6af0685a88',
      accessToken: this.getParameterByName('access_token'),
      config: [],
      artist: '',
      data: [],
      tracks: []
    }
  },
  methods: {
    getToken: function(){
      window.location = 'https://accounts.spotify.com/authorize?client_id=' + this.clientId + '&redirect_uri=http://localhost:8080&response_type=token';
    },
    getParameterByName: function(name, url) {
      if (!url) url = window.location.href;
      name = name.replace(/[\[\]]/g, "\\$&");
      var regex = new RegExp("[?#]" + name + "(=([^&#]*)|&|#|$)"),
          results = regex.exec(url);
      if (!results) return null;
      if (!results[2]) return '';
      return decodeURIComponent(results[2].replace(/\+/g, " "));
    },
    getArtist: function(){
      this.data = '';
      this.tracks = '';
      this.config = {headers: {'Authorization': 'Bearer ' + this.accessToken}};
      Axios.get('https://api.spotify.com/v1/search?q=' + this.artist + '&type=track,artist&market=US', this.config)
        .then(response => {
          this.data = response.data;
          this.tracks = response.data.tracks.items;
        })
        .catch(e => {
          console.log(error);
        })
    }
  }
};

</script>

<style lang="scss">
  .container {
    text-align: center;
    font-size: 1.5em;
    .child-div {
      background: red;
    }
    &--styled {
      color: black;
    }
  }
  input.input {
      padding: 1em;
      width: 20%;
  }

  track-listing {
      text-align: left;
      width: 75%;
      margin: 0 auto;
  }

  button.button.is-link.is-outlined.is-fullwidth {
      background: blue;
      color: white;
      padding: 1em;
      border: none;
  }

  a.panel-block.is-active {
      width: 100%;
      float: left;
      padding: 1em 0;
  }
</style>
