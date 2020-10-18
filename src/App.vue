<template>
  <div id="app">
		<div class="element" >
			<section class="element__container"
							 v-for="(beer, i) of beers" :key="i">
				<div class="element__head">
					<h3 class="element__heading">{{beer.name}}</h3>
					<p class="element__id">#{{beer.id}}</p>
				</div>
				<img class="element__img"
						 @load="loadedState"
						 :src="beer.image_url ? beer.image_url : '#' && loadedState()" alt="Beer image">
				<div class="element__main-text">
					<p class="element__tip">{{beer.brewers_tips}}</p>
					<p class="element__description">{{beer.description}}</p>
					<button class="element__button-edit"
									@click.prevent="openEdit = !openEdit">Edit</button>
					<button class="element__button-delete"
									@click.prevent="deleteElem(i)">Delete</button>
				</div>
				<div v-if="openEdit" class="element__edit">
					<div class="element__edit-name" contenteditable="true"
							 @input="edit($event, 'heading', i)">{{beer.name}}</div>
					<div class="element__edit-description" contenteditable="true"
							 @input="edit($event, 'description', i)">{{beer.description}}</div>
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
	data() {
		return {
			page: 1,
			beers: [],
			loadedItems: 1,
			loaded: false,
			openEdit: false,
		}
	},
	methods: {
		async fetchBeers() {
				await axios.get(`https://api.punkapi.com/v2/beers?page=${this.page}&limit=25`)
						.then(res => res.data.length === 0 ? this.page = false : this.beers.push(...res.data) && this.page++)
		},
		loadedState() {
			if (this.loadedItems > this.beers.length) return
			this.beers.length === this.loadedItems ? this.loaded = this.loadedItems++ : this.loaded = this.loadedItems++ && false
		},
		edit(ev, elem, id) {
			const description = document.querySelectorAll(`.element__${elem}`)[id]
			description.textContent = ev.target.innerText
		},
		deleteElem(i) {
			this.beers.splice(i, 1)
		},
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

	@media (max-width: 400px)
		display: flex
		flex-direction: column

	&__container
		display: grid
		position: relative
		grid-template: 120px 1fr / 1fr 2fr
		grid-gap: 20px
		padding: 20px

		@media (max-width: 400px)
			display: flex
			flex-direction: column

	&__head
		display: flex
		grid-column: 1 / -1
		align-items: center
		justify-content: center
		padding: 30px
		background-color: #7418aa

		@media (max-width: 400px)
			width: 100%
			box-sizing: border-box

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
		margin: 0 auto

	&__description
		font-size: 20px

	&__tip
		font-size: 24px

	&__button
		&-edit
			margin-right: 10%
			width: 40%
			height: 40px
			border-radius: 30px
			border: 1px solid white
			background-color: #e9b814
			font-size: 16px
			font-weight: bold
			outline: none

			&:hover
				cursor: pointer

		&-delete
			width: 40%
			height: 40px
			border-radius: 30px
			border: 1px solid white
			background-color: #db2d2d
			font-size: 16px
			font-weight: bold
			outline: none

			&:hover
				cursor: pointer

	&__edit
		grid-column: 1 / -1
		padding: 10px
		border: 3px solid
		background-color: white

		&-name
			margin-bottom: 20px
			font-weight: bold
			font-size: 20px
			outline: none

		&-description
			font-size: 18px
			outline: none

.loadMore
	display: block
	width: 150px
	height: 40px
	border: 1px solid white
	margin: 20px auto
	border-radius: 30px
	background-color: #2fcbec
	font-weight: bold
	font-size: 20px
	color: white

	&:hover
		cursor: pointer

</style>
