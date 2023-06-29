<script lang="ts">
	import ThemeToggle from '../components/ThemeToggle.svelte'

	let raceLengthMinutes = 20
	let raceLengthLaps = 10
	let fuelPerLap = 3.7

	let minutes = 1
	let seconds = 42
	let milliseconds = 690

	$: if (minutes < 0) minutes = 0

	$: if (seconds < 0) seconds = 0
	$: if (seconds > 59) seconds = 59

	$: if (milliseconds < 0) milliseconds = 0
	$: if (milliseconds > 999) milliseconds = 999

	$: computedMinutes = minutes + seconds / 60 + milliseconds / 60000
	$: laps =
		currentTab?.name === 'Time' ? Math.ceil(raceLengthMinutes / computedMinutes) : raceLengthLaps

	$: minimumFuel = Math.ceil(laps * fuelPerLap)
	$: recommendedFuel = Math.ceil(minimumFuel + fuelPerLap)
	$: safeFuel = Math.ceil(recommendedFuel + fuelPerLap)

	const tabs = [
		{ name: 'Time', current: true },
		{ name: 'Laps', current: false },
	]

	$: currentTab = tabs.find((el) => el.current)

	function handleTabChange(idx: number) {
		tabs.forEach((t, i) => (tabs[i].current = false))
		tabs[idx].current = true
	}

	function classNames(...classes: string[]) {
		return classes.filter(Boolean).join(' ')
	}
</script>

<main class="relative">
	<section class="min-w-screen relative flex min-h-screen items-center justify-center py-16 px-4">
		<div class="max-w-[32rem]">
			<ThemeToggle />
			<p class="text-center text-lg font-light dark:text-white">Sim Racing</p>
			<h1 class="mb-6 text-center text-4xl font-bold text-gray-900 dark:text-white">
				Fuel Calculator
			</h1>
			<nav
				class="isolate flex divide-x divide-gray-200 rounded-lg shadow dark:divide-gray-700"
				aria-label="Tabs">
				{#each tabs as tab, tabIdx}
					<button
						on:click={() => handleTabChange(tabIdx)}
						class={classNames(
							tab.current
								? 'text-gray-900 dark:text-white'
								: 'text-gray-500 hover:text-gray-700 dark:hover:text-white',
							tabIdx === 0 ? 'rounded-tl-lg' : '',
							tabIdx === tabs.length - 1 ? 'rounded-tr-lg' : '',
							'group relative min-w-0 flex-1 overflow-hidden bg-white dark:bg-gray-800 py-4 px-4 text-center text-sm font-medium hover:bg-gray-50 focus:z-10'
						)}
						aria-current={tab.current ? 'page' : undefined}>
						<span>{tab.name}</span>
						<span
							aria-hidden="true"
							class={classNames(
								tab.current ? 'bg-indigo-500' : 'bg-transparent',
								'absolute inset-x-0 bottom-0 h-0.5'
							)} />
					</button>
				{/each}
			</nav>
			<!-- Calculator -->
			<div class="flex flex-col space-y-6 bg-gray-50 p-8 dark:bg-gray-700 dark:text-white">
				{#if currentTab?.name === 'Time'}
					<div class="w-full">
						<label class="mb-1 block" for="race-length"
							>Race Length <span class="float-right text-xs">in minutes</span></label>
						<input
							type="number"
							name="race-length"
							id="race-length"
							class="block w-full min-w-0 flex-1 rounded-md border border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
							placeholder="Race Length"
							bind:value={raceLengthMinutes} />
					</div>
				{:else if currentTab?.name === 'Laps'}
					<div class="w-full">
						<label class="mb-1 block" for="race-length"
							>Race Length <span class="float-right text-xs">in laps</span></label>
						<input
							type="number"
							name="race-length"
							id="race-length"
							class="block w-full min-w-0 flex-1 rounded-md border border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
							placeholder="Race Length"
							bind:value={raceLengthLaps} />
					</div>
				{/if}

				<div class="w-full">
					<label for="fuel-per-lap" class="mb-1 block">Fuel per Lap</label>
					<input
						type="number"
						step="0.1"
						name="fuel-per-lap"
						id="fuel-per-lap"
						class="block w-full min-w-0 flex-1 rounded-md border border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
						placeholder="Fuel per Lap"
						bind:value={fuelPerLap} />
				</div>

				<div>
					<label for="lap-time-minutes" class="mb-1 block"
						>Lap Time <span class="float-right text-xs">m:s:ms</span></label>
					<div class="flex flex rounded-md shadow-sm">
						<input
							type="number"
							name="lap-time-minutes"
							id="lap-time-minutes"
							class="block w-full min-w-0 flex-1 rounded-none rounded-md rounded-l-md border border-r-0 border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
							placeholder="minutes"
							min="0"
							bind:value={minutes} />

						<span
							class="inline-flex items-center border border-r-0 border-gray-300 px-1 text-gray-500 dark:bg-gray-600 dark:text-white sm:text-sm"
							>:</span>
						<input
							type="number"
							name="lap-time-seconds"
							id="lap-time-seconds"
							class="block w-full min-w-0 flex-1 rounded-none rounded-md border border-r-0 border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
							placeholder="seconds"
							min="0"
							max="59"
							bind:value={seconds} />

						<span
							class="inline-flex items-center border border-r-0 border-gray-300 px-1 text-gray-500 dark:bg-gray-600 dark:text-white sm:text-sm"
							>:</span>
						<input
							type="number"
							name="lap-time-milliseconds"
							id="lap-time-milliseconds"
							class="block w-full min-w-0 flex-1 rounded-none rounded-md rounded-r-md border border-gray-300 py-1.5 px-2 text-gray-900 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 dark:bg-gray-600 dark:text-white sm:text-sm sm:leading-6"
							placeholder="milliseconds"
							min="0"
							max="999"
							bind:value={milliseconds} />
					</div>
				</div>
			</div>
			<div
				class="flex flex-col flex-wrap justify-between justify-between gap-y-4 rounded-b-lg bg-indigo-50 p-8 text-indigo-700 dark:bg-indigo-900 dark:text-indigo-200 sm:flex-row">
				<div class="grow basis-1/2">
					<p class="text-2xl font-bold">{recommendedFuel}l</p>
					<p class="text-sm">Recommended Fuel</p>
				</div>
				<div class="grow basis-1/2">
					<p class="text-2xl font-bold">{safeFuel}l</p>
					<p class="text-sm">Safe Fuel (Full Formation Lap)</p>
				</div>
				<div class="grow basis-1/2">
					<p class="text-2xl font-bold">{minimumFuel}l</p>
					<p class="text-sm">Minimum Fuel</p>
				</div>
				{#if currentTab?.name === 'Time'}
					<div class="grow basis-1/2">
						<p class="text-2xl font-bold">{laps}</p>
						<p class="text-sm">Total Laps</p>
					</div>
				{:else if currentTab?.name === 'Laps'}
					<div class="grow basis-1/2">
						<p class="text-2xl font-bold">{Math.ceil(laps * computedMinutes)}</p>
						<p class="text-sm">Total minutes</p>
					</div>
				{/if}
			</div>
		</div>
	</section>
	<footer class="absolute bottom-4 w-full text-center dark:text-white">
		<p class="text-xs font-light">
			Coded by <a
				href="https://timneubauer.dev"
				class="underline hover:text-indigo-600 dark:hover:text-indigo-400">timneubauer.dev</a>
		</p>
	</footer>
</main>
