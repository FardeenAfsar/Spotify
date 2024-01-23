<template>
  <div id="app">
    <Component
      :is="getCurrentComponent"
      :auth="auth"
      :endpoints="endpoints"
      :player="player"
      @spotifyTrackUpdated="updateCurrentTrack"
      @requestRefreshToken="requestRefreshTokens"
    ></Component>
  </div>
</template>

<script>
import Authorise from '@/components/Authorise'
import NowPlaying from '@/components/NowPlaying'

import { getStoredAuth, setStoredAuth } from '@/utils/utils.js'

export default {
  name: 'App',

  components: {
    Authorise,
    NowPlaying
  },

  props: {},

  data() {
    return {
      storedAuth: '',
      test: 'hello, world',
      auth: {
        status: false,
        clientId: process.env.VUE_APP_SP_CLIENT_ID,
        clientSecret: process.env.VUE_APP_SP_CLIENT_SECRET,
        authCode: 'AQAwbQWRc-t8nK2OhvCgjhY0FRfotaNw4GC_XPhgBI8_4jcr4u2_uLzzQcvS3sXGIrFtR6Ia4qXCKxAiQpDttCBfBouXU5mUliL0FN6DyAKtRD4mOH_YrcuRxbCoaUA7rybyllqinDUiVzRCIxo3gzsdEjfAsLbu2nTA9YIp46numtv2_ip4kpYehhd1XruHo8t-JGvw',
        accessToken: 'BQDk5UHjzApkPeN3lcPqp_7C9N0NHU2hUFI0uxKmQJIfVWh9iFne4x191NH-olJPd6Xc6R_3-xGcz5mxx7VyJay7NpAZVPpSw7AaDrxdP_0Y1g-oi_iKf0svULvyzay6nMr43Hep74NkmDoGTGs4lCBmE4TDX-5dHRoBiUji-LbqYw47h33gc2GKsCAzAWeBAmI0n7ihIRc',
        refreshToken: 'AQCGFQgdH9Re8YTSztQ1Li0w37zsWBOAZhy1jvdeMMibBmB4mHD2oz1IzC90VCBKe_swwww9HQPRuYxqhFBQ2HVraRkrcll_aZ1WbDG0-5lzZsyv-W_jvYjbXnvAhoyaT7c'
      },
      endpoints: {
        auth: 'https://accounts.spotify.com/authorize',
        token: 'https://accounts.spotify.com/api/token',
        base: 'https://api.spotify.com/v1',
        nowPlaying: 'me/player/currently-playing'
      },
      player: {
        playing: false,
        trackArtists: [],
        trackTitle: '',
        trackAlbum: []
      },
      storedId: ''
    }
  },

  computed: {
    /**
     * Check for the existence of a stored access token and
     * return the correct Component to be displayed.
     * @return {String}
     */
    getCurrentComponent() {
      return this.auth.status === false ? 'Authorise' : 'NowPlaying'
    }
  },

  created() {
    this.auth = {
      ...this.auth,
      ...getStoredAuth()
    }
  },

  mounted() {},

  methods: {
    /**
     * Store
     */
    storeAccessToken() {
      this.getAccessToken()
    },

    /**
     * Request a refresh token from Spotify.
     */
    requestRefreshTokens() {
      this.auth.status = false
    },

    /**
     * Update the player object.
     * @param {Object} value - Spotify playr object.
     */
    updateCurrentTrack(value) {
      this.player = value
    }
  },

  watch: {
    /**
     * Watch the authorisation status.
     */
    'auth.status': function() {
      setStoredAuth(this.auth)
    }
  }
}
</script>
