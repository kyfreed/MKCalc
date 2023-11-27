<script setup>
import { ref, onMounted, watch } from 'vue'

const NUM_OF_STATS = 13
const mode = ref("Closest")
const num_of_builds = ref(5)
const source_build = ref(["(Mario, Ludwig, Medium Mii)", "(Standard Kart, 300 SL Roadster, The Duke)", "(Standard, Blue Standard, GLA Tires)", "(Super Glider, Waddle Wing, Hylian Kite)"])
const source_character = ref("(Mario, Ludwig, Medium Mii)")
const source_vehicle = ref("(Standard Kart, 300 SL Roadster, The Duke)")
const source_wheels = ref("(Standard, Blue Standard, GLA Tires)")
const source_glider = ref("(Super Glider, Waddle Wing, Hylian Kite)")
const build_selected = ref(1)

const STAT_NAMES = ["Weight", "Acceleration", "Traction", "Mini-Turbo", "Ground Speed", "Water Speed", "Anti-Gravity Speed", "Air Speed", "Ground Handling", "Water Handling", "Anti-Gravity Handling", "Air Handling", "Invincibility"]

const CHARACTER_LIST = [
	"(Baby Peach, Baby Daisy)",
	"(Baby Rosalina, Lemmy)",
	"(Baby Mario, Baby Luigi, Dry Bones, Light Mii)",
	"(Toadette, Wendy, Isabelle)",
	"(Koopa Troopa, Lakitu, Bowser Jr.)",
	"(Toad, Shy Guy, Larry)",
	"(Cat Peach, Inkling Girl, Female Villager, Diddy Kong)",
	"(Peach, Daisy, Yoshi, Birdo, Peachette)",
	"(Tanooki Mario, Inkling Boy, Male Villager)",
	"(Mario, Ludwig, Medium Mii)",
	"(Luigi, Iggy, Kamek)",
	"(Rosalina, King Boo, Link, Pauline)",
	"(Metal Mario, Gold Mario, Pink Gold Peach, Petey Piranha)",
	"(Waluigi, Donkey Kong, Roy, Wiggler)",
	"(Wario, Dry Bowser, Funky Kong)",
	"(Bowser, Morton, Heavy Mii)"
]

const CHARACTER_STATS = [
	[0, 4, 5, 5, 1, 1, 1, 1, 10, 10, 10, 10, 6],
	[0, 5, 3, 5, 1, 1, 1, 1, 9, 9, 9, 9, 6],
	[1, 5, 4, 5, 2, 2, 2, 2, 8, 8, 8, 8, 5],
	[2, 5, 2, 4, 3, 3, 3, 3, 7, 7, 7, 7, 3],
	[2, 4, 5, 4, 3, 3, 3, 3, 8, 8, 8, 8, 4],
	[3, 4, 4, 4, 4, 4, 4, 4, 7, 7, 7, 7, 3],
	[3, 4, 3, 4, 5, 5, 5, 5, 6, 6, 6, 6, 3],
	[4, 3, 3, 4, 6, 6, 6, 6, 5, 5, 5, 5, 1],
	[5, 3, 1, 4, 6, 6, 6, 6, 5, 5, 5, 5, 1],
	[6, 2, 2, 3, 7, 7, 7, 7, 4, 4, 4, 4, 3],
	[6, 2, 1, 3, 7, 7, 7, 7, 5, 5, 5, 5, 3],
	[7, 1, 3, 2, 8, 8, 8, 8, 3, 3, 3, 3, 4],
	[10, 1, 1, 1, 8, 8, 8, 8, 3, 3, 3, 3, 3],
	[8, 1, 0, 1, 9, 9, 9, 9, 2, 2, 2, 2, 4],
	[9, 0, 1, 0, 10, 10, 10, 10, 1, 1, 1, 1, 5],
	[10, 0, 0, 0, 10, 10, 10, 10, 0, 0, 0, 0, 6]
]

const stats_to_consider = ref(Array(NUM_OF_STATS).fill(true))

const allowed_characters = ref(Array.from(Array(CHARACTER_LIST.length).keys())) //Get sequential array from 0 to the length of the CHARACTER_LIST

const VEHICLE_LIST = [
	"(Standard Kart, 300 SL Roadster, The Duke)",
	"(Pipe Frame, Varmint, City Tripper)",
	"(Mach 8, Sports Coupe, Inkstriker)",
	"(Steel Driver, Tri-Speeder, Bone Rattler)",
	"(Cat Cruiser, Comet, Yoshi Bike, Teddy Buggy)",
	"(Circuit Special, B-Dasher, P-Wing)",
	"(Badwagon, GLA, Standard ATV)",
	"(Prancer, Sport Bike, Jet Bike)",
	"(Biddybuggy, Mr Scooty)",
	"(Landship, Streetle)",
	"(Sneeker, Gold Standard)",
	"(W 25 Silver Arrow, Standard Bike, Flame Rider, Wild Wiggler)",
	"(Blue Falcon, Splat Buggy)",
	"(Tanooki Kart, Koopa Clown)"
]

const VEHICLE_STATS = [
	[2, 4, 3, 5, 3, 3, 3, 3, 3, 2, 3, 3, 3],
	[1, 6, 4, 6, 2, 3, 1, 1, 5, 4, 4, 2, 2],
	[3, 3, 4, 5, 3, 3, 5, 4, 2, 2, 4, 2, 1],
	[4, 1, 3, 3, 4, 5, 2, 0, 1, 5, 1, 1, 5],
	[2, 5, 3, 6, 2, 2, 3, 4, 4, 2, 3, 4, 3],
	[3, 1, 1, 3, 5, 1, 4, 2, 1, 1, 2, 0, 6],
	[4, 0, 5, 3, 5, 2, 3, 1, 0, 1, 1, 0, 6],
	[1, 2, 2, 4, 4, 3, 3, 3, 3, 3, 2, 3, 5],
	[0, 7, 4, 7, 0, 1, 2, 1, 5, 4, 5, 4, 0],
	[0, 6, 6, 6, 2, 5, 0, 2, 4, 5, 2, 3, 2],
	[2, 2, 0, 4, 4, 2, 3, 3, 3, 2, 3, 2, 5],
	[1, 5, 5, 5, 2, 2, 4, 3, 4, 3, 4, 3, 4],
	[0, 3, 3, 4, 4, 2, 4, 3, 2, 3, 5, 1, 3],
	[3, 2, 7, 5, 3, 4, 3, 3, 4, 4, 3, 3, 4]
]

const allowed_vehicles = ref(Array.from(Array(VEHICLE_LIST.length).keys()))

const WHEEL_LIST = [
	"(Standard, Blue Standard, GLA Tires)",
	"(Monster, Hot Monster)",
	"(Roller, Azure Roller)",
	"(Slim, Wood, Crimson Slim)",
	"(Slick, Cyber Slick)",
	"(Metal, Gold Tires)",
	"(Button, Leaf Tires)",
	"(Off-Road, Retro Off-Road, Triforce Tires)",
	"(Sponge, Cushion)"
]

const WHEEL_STATS = [
	[2, 4, 5, 4, 2, 3, 2, 3, 3, 3, 3, 3, 4],
	[4, 2, 7, 3, 3, 2, 2, 1, 0, 1, 0, 1, 6],
	[0, 6, 4, 6, 0, 3, 0, 3, 4, 4, 4, 4, 0],
	[2, 2, 1, 3, 3, 2, 4, 2, 4, 4, 3, 4, 5],
	[3, 1, 0, 2, 4, 0, 4, 0, 2, 0, 2, 1, 5],
	[4, 0, 2, 2, 4, 3, 1, 2, 2, 2, 1, 0, 6],
	[0, 5, 3, 5, 1, 2, 2, 2, 3, 3, 4, 2, 3],
	[3, 3, 6, 3, 3, 4, 2, 1, 1, 1, 2, 2, 5],
	[1, 4, 6, 5, 1, 1, 1, 4, 2, 1, 2, 3, 4]
]

const allowed_wheels = ref(Array.from(Array(WHEEL_LIST.length).keys()))

const GLIDER_LIST = [
	"(Super Glider, Waddle Wing, Hylian Kite)",
	"(Cloud Glider, Parachute, Flower Glider, Paper Glider)",
	"(Wario Wing, Plane Glider, Gold Glider)",
	"(Peach Parasol, Parafoil, Bowser Kite, MKTV Parafoil)"
]

const GLIDER_STATS = [
	[1, 1, 1, 1, 1, 1, 0, 2, 1, 0, 1, 1, 1],
	[0, 2, 1, 2, 0, 1, 1, 1, 1, 0, 1, 2, 0],
	[2, 1, 0, 1, 1, 0, 1, 2, 1, 1, 0, 1, 1],
	[1, 2, 0, 2, 0, 0, 1, 1, 1, 1, 0, 2, 0]
]

const allowed_gliders = ref(Array.from(Array(GLIDER_LIST.length).keys()))

const builds = ref([])
const errors = ref([])
const abs_errors = ref([])

function recalculate() {
	source_build.value = [source_character.value, source_vehicle.value, source_wheels.value, source_glider.value]
	if (mode.value == "Closest") {
		builds.value = []
		errors.value = []
		abs_errors.value = []
		const builds_with_errors = calculate_closest_build()
		for (var i = 0; i < builds_with_errors.length; i++) {
			builds.value.push(builds_with_errors[i][0])
			errors.value.push(builds_with_errors[i][1])
			abs_errors.value.push(builds_with_errors[i][2])
		}
	}
}

function calculate_closest_build() {
	console.log("Function call", allowed_characters.value, allowed_vehicles.value)
	let arr = []
	for (var i = 0; i < allowed_characters.value.length; i++) {
		for (var j = 0; j < allowed_vehicles.value.length; j++) {
			for (var k = 0; k < allowed_wheels.value.length; k++) {
				for (var l = 0; l < allowed_gliders.value.length; l++) {
					let test_build =
						[
							CHARACTER_LIST[allowed_characters.value[i]],
							VEHICLE_LIST[allowed_vehicles.value[j]],
							WHEEL_LIST[allowed_wheels.value[k]],
							GLIDER_LIST[allowed_gliders.value[l]]
						]

					arr.push([
						test_build,
						calc_squared_error(source_build.value, test_build),
						calc_abs_error(source_build.value, test_build)
					])
				}
			}
		}
	}

	arr.sort((a, b) => {
		if (a[1] < b[1]) {
			return -1
		} else if (a[1] > b[1]) {
			return 1
		}
		return 0
	})

	let top_builds = []
	for (var i = 0; i < Math.min(num_of_builds.value, arr.length); i++) {
		top_builds.push(arr[i])
	}

	return top_builds
}

function calc_squared_error(source, test) {
	let checked_stats = 0
	let total_error = 0
	for (var i = 0; i < stats_to_consider.value.length; i++) {
		if (stats_to_consider.value[i]) {
			checked_stats++
			total_error += Math.pow(stat_for_build(test, i) - stat_for_build(source, i), 2)
		}
	}

	return Math.sqrt(total_error / checked_stats)
}

function calc_abs_error(source, test) {
	let checked_stats = 0
	let total_error = 0
	for (var i = 0; i < stats_to_consider.value.length; i++) {
		if (stats_to_consider.value[i]) {
			checked_stats++
			total_error += Math.abs(stat_for_build(test, i) - stat_for_build(source, i))
		}
	}

	return total_error / checked_stats
}

function stat_for_build(build, stat) {
	CHARACTER_STATS[CHARACTER_STATS.indexOf(build[0])]
	return CHARACTER_STATS[CHARACTER_LIST.indexOf(build[0])][stat] + VEHICLE_STATS[VEHICLE_LIST.indexOf(build[1])][stat] + WHEEL_STATS[WHEEL_LIST.indexOf(build[2])][stat] + GLIDER_STATS[GLIDER_LIST.indexOf(build[3])][stat]
}

function stats_to_string(build) {
	let stat_string = ""
	for (var i = 0; i < NUM_OF_STATS; i++) {
		stat_string += STAT_NAMES[i] + ": " + ((stat_for_build(build, i) + 3) / 4) + "<br>" // Convert stats to bars
	}
	return stat_string
}

function build_to_string(build) {
	return "Character: " + build[0] + "<br>Vehicle: " + build[1] + "<br>Wheels: " + build[2] + "<br>Glider: " + build[3]
}

function update_stats_to_consider(id) {
	if (stats_to_consider.value[id] == 0) {
		stats_to_consider.value[id] == 1
	} else {
		stats_to_consider.value[id] == 0
	}
	recalculate()
}


onMounted(() => {
	recalculate()
})

watch(source_character, (newvalue) => recalculate())
watch(source_vehicle, (newvalue) => recalculate())
watch(source_wheels, (newvalue) => recalculate())
watch(source_glider, (newvalue) => recalculate())

</script>


<template>
	<h2>Setup</h2>
	<div v-if="mode == 'Closest'">
		<v-expansion-panels>
			<v-expansion-panel>
				<v-expansion-panel-title>Source build</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-select label="Character" v-model="source_character" :items=CHARACTER_LIST style="max-width:1000px;">
						<template v-slot:item="{ item, props }">
							<v-list-item v-bind="props" :title="null">
								<v-list-item-title class="wrap-text">{{ item.title }}</v-list-item-title>
							</v-list-item>
						</template>
					</v-select>
					<v-select label="Vehicle" v-model="source_vehicle" :items=VEHICLE_LIST style="max-width:1000px;">
						<template v-slot:item="{ item, props }">
							<v-list-item v-bind="props" :title="null">
								<v-list-item-title class="wrap-text">{{ item.title }}</v-list-item-title>
							</v-list-item>
						</template>
					</v-select>
					<v-select label="Wheels" v-model="source_wheels" :items=WHEEL_LIST style="max-width:1000px;">
						<template v-slot:item="{ item, props }">
							<v-list-item v-bind="props" :title="null">
								<v-list-item-title class="wrap-text">{{ item.title }}</v-list-item-title>
							</v-list-item>
						</template>
					</v-select>
					<v-select label="Glider" v-model="source_glider" :items=GLIDER_LIST style="max-width:1000px;">
						<template v-slot:item="{ item, props }">
							<v-list-item v-bind="props" :title="null">
								<v-list-item-title class="wrap-text">{{ item.title }}</v-list-item-title>
							</v-list-item>
						</template>
					</v-select>
				</v-expansion-panel-text>
			</v-expansion-panel>
			<v-expansion-panel>
				<v-expansion-panel-title>Stats to consider</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-checkbox v-for="(stat, index) in STAT_NAMES" v-model="stats_to_consider[index]"
						@change="update_stats_to_consider(index)" :label=stat color="primary"></v-checkbox>
				</v-expansion-panel-text>
			</v-expansion-panel>
		</v-expansion-panels>
	</div>
	<div>
		<h2>Constraints</h2>
		<v-expansion-panels>
			<v-expansion-panel>
				<v-expansion-panel-title>
					Allowed characters
				</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-checkbox v-for="(character, index) in CHARACTER_LIST" @change="recalculate()"
						v-model="allowed_characters" :label=character :value=index color="primary"></v-checkbox>
				</v-expansion-panel-text>
			</v-expansion-panel>
			<v-expansion-panel>
				<v-expansion-panel-title>
					Allowed vehicles
				</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-checkbox v-for="(vehicle, index) in VEHICLE_LIST" @change="recalculate()" v-model="allowed_vehicles"
						:label=vehicle :value=index color="primary"></v-checkbox>
				</v-expansion-panel-text>
			</v-expansion-panel>
			<v-expansion-panel>
				<v-expansion-panel-title>
					Allowed wheels
				</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-checkbox v-for="(wheel, index) in WHEEL_LIST" @change="recalculate()" v-model="allowed_wheels"
						:label=wheel :value=index color="primary"></v-checkbox>
				</v-expansion-panel-text>
			</v-expansion-panel>
			<v-expansion-panel>
				<v-expansion-panel-title>
					Allowed gliders
				</v-expansion-panel-title>
				<v-expansion-panel-text>
					<v-checkbox v-for="(glider, index) in GLIDER_LIST" @change="recalculate()" v-model="allowed_gliders"
						:label=glider :value=index color="primary"></v-checkbox>
				</v-expansion-panel-text>
			</v-expansion-panel>
		</v-expansion-panels>
	</div>
	<div>
		<v-text-field @change="recalculate()" v-model="num_of_builds" type="number" label="Number of builds to show"
			style="width:300px"></v-text-field>
	</div>
	<div>
		<div>
			<h2 v-if="mode == 'Closest'">Source build stats</h2>
			<v-card v-if="mode == 'Closest'">
				<v-card-text>
					<h3 v-html="build_to_string(source_build)"></h3>
					<br>
					<p v-html="stats_to_string(source_build)"></p>
				</v-card-text>
			</v-card>
		</div>
		<br>
		<h2>{{ (mode == "Closest") ? "Closest builds" : "Optimized builds" }}</h2>
		<div v-for="(build, index) in builds">
			<v-card v-if="build_selected == index + 1">
				<v-card-text>
					<h3 v-html="build_to_string(build)"></h3>
					<br>
					<p v-html="stats_to_string(build)">
					</p>
					<hr style="width:200px">
					<p v-if="mode == 'Closest'">
						RMSE: {{ (errors[index] / 4).toFixed(4) }}<br>
						MAE: {{ (abs_errors[index] / 4).toFixed(4) }}
					</p>
				</v-card-text>
			</v-card>
		</div>
		<v-pagination v-model="build_selected" :length=builds.length></v-pagination>
	</div>
	<div>
		<v-radio-group @change="recalculate()" v-model="mode">
			<v-radio label="Closest build" value="Closest" color="primary"></v-radio>
			<v-radio label='Optimized build (coming "soon")' value="Optimized" color="primary" disabled></v-radio>
		</v-radio-group>
	</div>
</template>

<style scoped>
.wrap-text {
	-webkit-line-clamp: none !important;
	white-space: normal;
}
</style>

<style>
@import url("https://cdn.jsdelivr.net/npm/@mdi/font@5.x/css/materialdesignicons.min.css");
</style>
