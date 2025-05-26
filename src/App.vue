mikrotik_parser
<template>
	<div style="display: flex">
		<span v-text="'Search'"/>
		<input
			type="radio"
			id="byDomain"
			value="byDomain"
			v-model="this.pickedSearchType"
			checked
		/>
		<label
			for="byDomain"
			v-text="'by domain'"
		/>
		<input
			type="radio"
			id="bySrcAddress"
			value="bySrcAddress"
			v-model="this.pickedSearchType"
		/>
		<label
			for="bySrcAddress"
			v-text="'by src address'"
		/>
	</div>
	<span
		style="display: flex"
	>
		<span
			v-if="this.pickedSearchType === 'byDomain'"
			v-text="'DNS filter:'"
		/>
		<span
			v-else-if="this.pickedSearchType === 'bySrcAddress'"
			v-text="'Src address filter:'"
		/>
		<input
			type="text"
			v-model="this.filterStr"
			ref="input"
		>
		<button @click="filter">Search</button>
	</span>
	<div
		v-for="domain in domains"
		:key="domain.dstDns"
		style="margin-bottom: 40px"
	>
		<div
			v-text="domain.dstDns"
		/>
		<span
			v-for="element in domain.dnsConnections"
			:key="element.dstDNS"
		>
			<span
				v-text="element.dstIP"
			/>
			<span
				v-text="', '"
			/>
		</span>
		<span style="display: flex">
			<span
				v-text="'ignore vpn:'"
			/>
			<input
				type="checkbox"
				:id="domain.id"
				v-model="domain.isIgnoreVpn"
				@input="this.onCheckbox(domain)"
			>
		</span>
	</div>
</template>


<script>
import axios from 'axios'

export default {

	data: () => ({
		domains: [],
		filterStr: '',
		pickedSearchType: '',
	}),

	methods: {
		filter() {
			let filter = this.filterStr
			this.updateQueryParam('filter', filter)
			switch(this.pickedSearchType) {
				case 'byDomain': {
					axios.get(`/api/v1/dns?find=${filter}`)
						.then(response => {
							this.domains = response.data
						})
						.catch(e => {
							console.log(e)
						})
					break
				}
				case 'bySrcAddress': {
					axios.get(`/api/v1/src?srcIp=${filter}`)
						.then(response => {
							this.domains = response.data
						})
						.catch(e => {
							console.log(e)
						})
					break
				}
			}

		},
		onCheckbox(element) {
			axios.post(`/api/v1/dns?dns=${element.dstDns}&enabled=${!element.isIgnoreVpn}`)
				.then(answer => {
				})
				.catch(e => {
					console.log(e)
				})
		},
		updateQueryParam(key, value) {
			const url = new URL(window.location);
			if (value === null || value === undefined) {
				url.searchParams.delete(key);
			} else {
				url.searchParams.set(key, value);
			}
			window.history.replaceState({}, '', url); // адрес обновится, страница НЕ перезагрузится
		},
	},

	mounted() {
		const params = new URLSearchParams(window.location.search)
		this.filterStr = params.get('filter')
		if (this.filterStr) {
			this.filter()
		}
	}
}
</script>


<style scoped>
</style>
