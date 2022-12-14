<template>
  <div>
    <h1 class="corn-font primary--text">
      Las más populares entre los usuarios
    </h1>
    <v-carousel
      v-if="mostPopularFilms.length"
      v-model="carouselPosition"
      hide-delimiter-background
      hide-delimiters
      :show-arrows="false"
      :cycle="automaticCarousel"
      height="auto"
    >
      <v-carousel-item
        v-for="index in totalItemsInCarouselMostPopularMovies"
        :key="index"
        class="pt-5"
      >
        <v-row
          class="pb-5 px-2"
        >
          <v-col
            v-for="movie in getMoviesToShow(carouselPosition)"
            :key="movie.id"
            :cols="12 / totalPopularFilms"
            class="d-flex px-2" style="flex-direction:column"
          >
            <v-card class="flex-grow-1" min-height="400px">
              <v-img
                :src="`https://image.tmdb.org/t/p/w220_and_h330_face/${movie.posterPath}`"
                gradient="to bottom, transparent, rgba(0,0,0,.75)"
                class="primary--text align-end"
                max-height="200"
                position="top center"
              >
                <v-card-title>
                  <p class="subtitle-1 font-weight-bold mb-0">
                    {{ movie.title }}
                    <span class="caption primary--text" v-if="movie.releaseDate">
                      ({{ movie.releaseDate.substr(0,4) }})
                    </span>
                  </p>
                </v-card-title>
              </v-img>
              <v-card-text>
                <v-row dense>
                  <v-col cols="6">
                    <v-rating
                      dense
                      color="primary"
                      background-color="grey lighten-1"
                      half-increments
                      readonly
                      :value="movie.voteAverage"
                    />
                  </v-col>
                  <v-col cols="6" class="text-right">
                    <span class="caption yellow--text text--darken-2">
                      {{ movie.voteAverage}}
                    </span>
                  </v-col>
                  <v-col cols="12">
                    <v-chip-group
                      column
                    >
                      <v-chip
                        v-for="genre in movie.genreIds"
                        :key="genre"
                        disabled
                        x-small
                        class="text-caption mx-1"
                      >
                        {{ genre }}
                      </v-chip>
                    </v-chip-group>
                  </v-col>
                  <v-col cols="12">
                    <p class="reduce_text">{{ movie.overview }}</p>
                  </v-col>
                </v-row>
              </v-card-text>
              <v-card-actions class="mt-5">
                <span
                  v-if="movie.adult"
                  class="overline error--text"
                  style="position: absolute;bottom: 10px;left: 20px;"
                >
                  +18
                </span>
                <v-spacer/>
                <v-btn
                  color="primary"
                  tile
                  text
                  style="position: absolute;bottom: 10px;right: 10px;"
                  @click="goTo('movie-api-details', { movieId: movie.id })"
                >
                  VER
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-carousel-item>
      <div class="width_full text-center">
        <v-progress-linear
          :value="(carouselPosition+1)*100/totalItemsInCarouselMostPopularMovies"
        />
      </div>
      <div class="width_full text-center">
        <v-btn
          icon
          color="primary"
          large
          @click="changeCarouselPosition(carouselPosition -= 1)"
        >
          <v-icon>
            skip_previous
          </v-icon>
        </v-btn>
        <v-btn
          icon
          color="primary"
          large
          class="mx-5"
          @click="automaticCarousel = !automaticCarousel"
        >
          <v-icon v-if="!automaticCarousel">
            play_arrow
          </v-icon>
          <v-icon v-if="automaticCarousel">
            pause
          </v-icon>
        </v-btn>
        <v-btn
          icon
          color="primary"
          large
          @click="changeCarouselPosition(carouselPosition += 1)"
        >
          <v-icon>
            skip_next
          </v-icon>
        </v-btn>
      </div>
    </v-carousel>
    <h1 class="corn-font primary--text mt-10">Buscador</h1>
    <v-row class="mt-5 mb-10">
      <v-col cols="12">
        <v-text-field
          v-model="search"
          placeholder="Buscador..."
          outlined
          append-icon="search"
          rounded
          dense
          @input="searchMovie"
        />
      </v-col>
      <v-col cols="12">
        <v-row align="center">
          <v-col cols="12" v-if="!moviesSearch.length">
            <v-img
              src="@/assets/img/palomita_search.png"
              height="400"
              contain
            />
          </v-col>
          <v-col cols="6" md="3" v-for="movie in moviesSearch" :key="movie.id">
            <v-card class="flex-grow-1" min-height="350px">
              <v-img
                :src="`https://image.tmdb.org/t/p/w220_and_h330_face/${movie.posterPath}`"
                gradient="to bottom, transparent, rgba(0,0,0,.75)"
                class="primary--text align-end"
                max-height="200"
                position="center center"
              >
                <v-card-title>
                  <p class="subtitle-1 font-weight-bold mb-0">
                    {{ movie.title }}
                    <span class="caption primary--text" v-if="movie.releaseDate">
                      ({{ movie.releaseDate.substr(0,4) }})
                    </span>
                  </p>
                </v-card-title>
              </v-img>
              <v-card-text>
                <v-row dense>
                  <v-col cols="6">
                    <v-rating
                      dense
                      color="primary"
                      background-color="grey lighten-1"
                      half-increments
                      readonly
                      :value="movie.voteAverage * 5 / 10"
                    />
                  </v-col>
                  <v-col cols="12">
                    <v-chip-group
                      column
                    >
                      <v-chip
                        v-for="genre in movie.genreIds"
                        :key="genre"
                        disabled
                        x-small
                        class="text-caption mx-1"
                      >
                        {{ genre }}
                      </v-chip>
                    </v-chip-group>
                  </v-col>
                </v-row>
              </v-card-text>
              <v-card-actions class="mt-5">
                <span
                  v-if="movie.adult"
                  class="overline error--text"
                  style="position: absolute;bottom: 10px;left: 20px;"
                >
                  +18
                </span>
                <v-spacer/>
                <v-btn
                  color="primary"
                  tile
                  text
                  style="position: absolute;bottom: 10px;right: 10px;"
                  @click="goTo('movie-api-details', { movieId: movie.id })"
                >
                  VER
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import { getPopularMovies, searchMovie } from '@/infrastructure/application/movie';
import ListPopularMovieQuery
  from '@/application/movie/query/listPopularMovies/ListPopularMovieQuery';
import SearchMovieQuery from '@/application/movie/query/searchMovie/SearchMovieQuery';

export default {
  name: 'home',
  computed: {
    totalItemsInCarouselMostPopularMovies() {
      return this.mostPopularFilms.length / this.totalPopularFilms;
    },
    totalPopularFilms() {
      let totalFilmsToShow = 4;
      if (this.$vuetify.breakpoint.xs) {
        totalFilmsToShow = 1;
      } else if (this.$vuetify.breakpoint.sm) {
        totalFilmsToShow = 2;
      }

      return totalFilmsToShow;
    },
  },
  data() {
    return {
      mostPopularFilms: [],
      carouselPosition: 0,
      automaticCarousel: true,
      search: '',
      moviesSearch: [],
    };
  },
  methods: {
    async getMostPopularsMovie() {
      const response = await getPopularMovies.invoke(new ListPopularMovieQuery());

      if (!response.success) {
        return;
      }

      this.mostPopularFilms = response.movies;
    },
    getMoviesToShow(totalFilmsToShow) {
      return this.mostPopularFilms.slice(
        totalFilmsToShow * this.totalPopularFilms,
        totalFilmsToShow * this.totalPopularFilms + this.totalPopularFilms,
      );
    },
    changeCarouselPosition(nextPosition) {
      if (nextPosition < 0) {
        this.carouselPosition = this.totalItemsInCarouselMostPopularMovies - 1;
        return;
      }

      if (nextPosition >= this.totalItemsInCarouselMostPopularMovies) {
        this.carouselPosition = 0;
        return;
      }

      this.carouselPosition = nextPosition;
    },
    async searchMovie() {
      if (this.search.trim().length < 3) {
        return;
      }

      const response = await searchMovie.invoke(new SearchMovieQuery(this.search));
      if (!response.success) {
        return;
      }

      this.moviesSearch = response.movies;
    },
    goTo(nameRoute, params) {
      this.$router.push({ name: nameRoute, params });
    },
  },
  beforeMount() {
    this.getMostPopularsMovie();
  },
};
</script>
