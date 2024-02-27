<template>
	<input
		type="text"
		@change="this.filter($event)"
	>
	<div
		class="src"
		v-for="element in elements"
	>
		<p
			v-text="element.dstIP"
		/>
		<p
			v-text="element.dstDNS"
		/>
		<span style="display: flex">
			<span
				v-text="'ignore vpn:'"
			/>
			<input
				type="checkbox"
				:id="element.id"
				v-model="element.isIgnoreVpn"
				@input="this.onCheckbox(element)"
				style="margin-bottom: 40px"
			>
		</span>
	</div>
</template>


<script>
import axios from 'axios'

export default {

	components: {},

	methods: {
		filter(event) {
			axios
				.get(`http://localhost:8080/api/v1/dns?find=${event.target.value}`)
				.then(response => {
					this.elements = response.data
				})
				.catch(e => {
					console.log(e)
				})
		},

		onCheckbox(element) {
			axios
				.post(`http://localhost:8080/api/v1/dns?add=${element.dstDNS}&enabled=${!element.isIgnoreVpn}`)
				.then(answer => {

				})
				.catch(e => {
					console.log(e)
				})
		}

	},

	data: () => ({
		elements: []

	})
}
</script>


<style scoped>
</style>
