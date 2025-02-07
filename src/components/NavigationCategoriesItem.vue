<template>
	<NcAppNavigationItem
		:title="title"
		class="app-navigation-noclose separator-below"
		:class="{ 'category-header': selectedCategory !== null }"
		:open.sync="open"
		:allow-collapse="true"
		@click.prevent.stop="onToggleCategories"
	>
		<FolderIcon slot="icon" :size="20" />
		<NcAppNavigationItem
			:title="t('notes', 'All notes')"
			@click.prevent.stop="onSelectCategory(null)"
		>
			<HistoryIcon slot="icon" :size="20" />
			<NcAppNavigationCounter slot="counter">
				{{ numNotes }}
			</NcAppNavigationCounter>
		</NcAppNavigationItem>

		<NcAppNavigationItem v-for="category in categories"
			:key="category.name"
			:title="categoryTitle(category.name)"
			@click.prevent.stop="onSelectCategory(category.name)"
		>
			<FolderOutlineIcon v-if="category.name === ''" slot="icon" :size="20" />
			<FolderIcon v-else slot="icon" :size="20" />
			<NcAppNavigationCounter slot="counter">
				{{ category.count }}
			</NcAppNavigationCounter>
		</NcAppNavigationItem>
	</NcAppNavigationItem>
</template>

<script>
import {
	NcAppNavigationItem,
	NcAppNavigationCounter,
} from '@nextcloud/vue'

import FolderIcon from 'vue-material-design-icons/Folder.vue'
import FolderOutlineIcon from 'vue-material-design-icons/FolderOutline.vue'
import HistoryIcon from 'vue-material-design-icons/History.vue'

import { getCategories } from '../NotesService.js'
import { categoryLabel } from '../Util.js'
import store from '../store.js'

export default {
	name: 'NavigationCategoriesItem',

	components: {
		FolderIcon,
		FolderOutlineIcon,
		HistoryIcon,
		NcAppNavigationItem,
		NcAppNavigationCounter,
	},

	props: {
		selectedCategory: {
			type: String,
			default: null,
		},
	},

	data() {
		return {
			open: false,
		}
	},

	computed: {
		numNotes() {
			return store.getters.numNotes()
		},

		categories() {
			return getCategories(1, true)
		},

		title() {
			return this.selectedCategory === null ? this.t('notes', 'Categories') : categoryLabel(this.selectedCategory)
		},
	},

	methods: {
		categoryTitle(category) {
			return categoryLabel(category)
		},

		onToggleCategories() {
			this.open = !this.open
		},

		onSelectCategory(category) {
			this.open = false
			this.$emit('category-selected', category)
		},
	},
}
</script>
<style scoped>
.separator-below {
	border-bottom: 1px solid var(--color-border);
}
</style>
