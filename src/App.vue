<template>
  <div id="app">
		<div class="element" >
			<section class="element__container"
							 v-for="beer of beers" :key="beer.id">
				<div class="element__head">
					<h3 class="element__heading">{{beer.name}}</h3>
					<p class="element__id">#{{beer.id}}</p>
				</div>
				<img class="element__img"
						 @load="loadedState"
						 :src="beer.image_url" alt="Beer image">
				<div class="element__main-text">
					<p class="element__tip">{{beer.brewers_tips}}</p>
					<p class="element__description">{{beer.description}}</p>
					<button class="element__button-edit">Edit</button>
					<button class="element__button-delete">Delete</button>
				</div>
			</section>
		</div>
		<button class="loadMore"
						v-if="page"
						@click.prevent="fetchBeers">{{loaded ? 'Show next' : 'Loading'}}</button>
  </div>
</template>

<script>


export default {
  name: 'App',
  components: {

  },
	data() {
		return {
			page: 1,
			beers: [],
			loadedItems: 1,
			loaded: false
		}
	},
	methods: {
		async fetchBeers() {
				await axios.get(`https://api.punkapi.com/v2/beers?page=${this.page}&limit=25`)
						.then(res => res.data.length === 0 ? this.page = false : this.beers.push(...res.data) && this.page++)
		},
		loadedState() {
			this.beers.length === this.loadedItems ? this.loaded = this.loadedItems++ : this.loaded = this.loadedItems++ && false
		}
	},
	  mounted() {
		 this.fetchBeers()
	},

}
</script>

<style lang="sass">

.element
	display: grid
	grid-template-columns: repeat(auto-fit, minmax(400px, 1fr))

	&__container
		display: grid
		grid-template: 120px 1fr / 1fr 2fr
		grid-gap: 20px
		padding: 20px

	&__head
		display: flex
		grid-column: 1 / -1
		align-items: center
		justify-content: center
		padding: 30px
		background-color: #7418aa

	&__heading
		margin-right: 20px
		font-size: 30px
		text-align: center
		color: white

	&__id
		font-size: 25px
		color: white

	&__img
		width: 150px

	&__description
		font-size: 20px

	&__tip
		font-size: 24px

	&__button
		&-edit
			margin-right: 10px
			width: 40%
			height: 30px
			border-radius: 30px
			border: 1px solid white
			background-color: #e9b814
			font-size: 16px
			font-weight: bold
		&-delete
			width: 40%
			height: 30px
			border-radius: 30px
			border: 1px solid white
			background-color: #db2d2d
			font-size: 16px
			font-weight: bold

.loadMore
	display: block
	width: 150px
	height: 40px
	border: 1px solid white
	margin: 20px auto
	border-radius: 30px
	background-color: #2fcbec

</style>
