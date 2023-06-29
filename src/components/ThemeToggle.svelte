<script lang="ts">
	import { onMount } from 'svelte'

	let enabled = false
	let mounted = false

	onMount(() => {
		let theme = localStorage.getItem('theme')

		if (
			theme === 'dark' ||
			(!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)
		) {
			enabled = true
		} else {
			enabled = false
		}

		mounted = true
	})

	function toggleTheme() {
		enabled = !enabled

		const theme = window.localStorage.getItem('theme')

		if (theme === 'light') {
			localStorage.setItem('theme', 'dark')
			document.documentElement.classList.add('dark')
		} else {
			localStorage.setItem('theme', 'light')
			document.documentElement.classList.remove('dark')
		}
	}
</script>

{#if mounted}
	<div class="absolute top-8 right-4">
		<button
			type="button"
			class="relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-indigo-600 focus:ring-offset-2 {enabled
				? 'bg-indigo-800'
				: 'bg-white'}"
			role="switch"
			aria-checked="false"
			on:click={toggleTheme}>
			<span class="sr-only">Darkmode Settings</span>

			<span
				class="pointer-events-none relative inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out {enabled
					? 'translate-x-5'
					: 'translate-x-0'}">
				<span
					class="absolute inset-0 flex h-full w-full items-center justify-center transition-opacity {enabled
						? 'opacity-0 duration-100 ease-out'
						: 'opacity-100 duration-200 ease-in'}"
					aria-hidden="true">
					<svg
						class="h-3 w-3"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						viewBox="0 0 24 24"
						xmlns="http://www.w3.org/2000/svg"
						aria-hidden="true">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							d="M12 3v2.25m6.364.386l-1.591 1.591M21 12h-2.25m-.386 6.364l-1.591-1.591M12 18.75V21m-4.773-4.227l-1.591 1.591M5.25 12H3m4.227-4.773L5.636 5.636M15.75 12a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0z" />
					</svg>
				</span>

				<span
					class="absolute inset-0 flex h-full w-full items-center justify-center rounded-full transition-opacity dark:bg-indigo-400 dark:text-white {enabled
						? 'opacity-100 duration-200 ease-in'
						: 'opacity-0 duration-100 ease-out'}"
					aria-hidden="true">
					<svg
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						viewBox="0 0 24 24"
						xmlns="http://www.w3.org/2000/svg"
						aria-hidden="true"
						class="h-3 w-3">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							d="M21.752 15.002A9.718 9.718 0 0118 15.75c-5.385 0-9.75-4.365-9.75-9.75 0-1.33.266-2.597.748-3.752A9.753 9.753 0 003 11.25C3 16.635 7.365 21 12.75 21a9.753 9.753 0 009.002-5.998z" />
					</svg>
				</span>
			</span>
		</button>
	</div>
{/if}
