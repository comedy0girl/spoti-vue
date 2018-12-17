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
           
              <p class="panel-heading">
                Search Artists
              </p>
              <div class="panel-block">
                <p class="control has-icons-left">
                  <input v-model="artist" @keyup.enter="getArtist" class="input" type="text" placeholder="Search for your faves">
                </p>
              </div>


               <div class="artist-listing">
                <a v-for="artist in artists" class="panel-block is-active">
                  <li><a href="#">{{ artist.name }}</a>
                      <img v-if="artist.images.length" :src="artist.images[0].url" :alt="artist.name">
                      <p v-else>No image available</p>
                  </li>
                </a>
              </div>



              <!-- <div class="track-listing">
              <a v-for="track in tracks" class="panel-block is-active">
                  {{ track.name }}
                  {{ track.artists[0].name }}
                  <audio
                    :src="track.preview_url"
                    controls>
                    Your browser does not support the <code>audio</code> element.
                  </audio>
              </a>
            </div> -->
        
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
      artists: [],
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
      this.artists = '';
      this.config = {headers: {'Authorization': 'Bearer ' + this.accessToken}};
      Axios.get('https://api.spotify.com/v1/search?q=' + this.artist + '&type=track,artist&market=US', this.config)
        .then(response => {
          this.data = response.data;
          this.artists = response.data.artists.items;
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