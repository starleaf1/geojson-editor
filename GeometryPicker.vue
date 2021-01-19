<template>
	<v-menu bottom v-model="menuOpen">
		<template #activator="{ on }">
			<v-btn color="accent" v-on="on">{{ value }} <v-icon right>mdi-chevron-down</v-icon></v-btn>
		</template>

		<v-list>
			<v-list-item two-line @click="$emit('input', 'Point')">
				<v-list-item-content>
					<v-list-item-title>Point</v-list-item-title>
					<v-list-item-subtitle>Lokasi berupa titik</v-list-item-subtitle>
				</v-list-item-content>
			</v-list-item>
			<v-list-item two-line @click="$emit('input', 'LineString')">
				<v-list-item-content>
					<v-list-item-title>LineString</v-list-item-title>
					<v-list-item-subtitle>Untuk jalan, saluran air, pagar, dll.</v-list-item-subtitle>
				</v-list-item-content>
			</v-list-item>
			<v-list-item two-line @click="$emit('input', 'Polygon')">
				<v-list-item-content>
					<v-list-item-title>Polygon</v-list-item-title>
					<v-list-item-subtitle>Lokasi berupa wilayah</v-list-item-subtitle>
				</v-list-item-content>
			</v-list-item>
		</v-list>
	</v-menu>
</template>

<script>
export default {
	name: 'GeometryPicker',

	props: {
		value: {
			type: String,
			default: () => 'Point'
		}
	},

	data () {
		return ({
			menuOpen: false
		})
	}
}
</script>
