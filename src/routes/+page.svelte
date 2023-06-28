<script lang="ts">
	/*
  This example requires some changes to your config:
  
  ```
  // tailwind.config.js
  module.exports = {
    // ...
    plugins: [
      // ...
      require('@tailwindcss/forms'),
    ],
  }
  ```
*/
	$: raceLengthMinutes = 20

	$: raceLengthLaps = 10

	$: fuelPerLap = 3.7

	$: minutes = 1
	$: seconds = 42
	$: milliseconds = 690

	$: if (minutes < 0) minutes = 0

	$: if (seconds < 0) seconds = 0
	$: if (seconds > 59) seconds = 59

	$: if (milliseconds < 0) milliseconds = 0
	$: if (milliseconds > 999) milliseconds = 999

	$: laps = Math.ceil(raceLengthMinutes / (minutes + seconds / 60 + milliseconds / 60000))

	$: minimumFuel = Math.ceil(laps * fuelPerLap)
	$: recommendedFuel = Math.ceil(minimumFuel + fuelPerLap)
	$: safeFuel = Math.ceil(recommendedFuel + fuelPerLap)

	const tabs = [
		{ name: 'Time', current: true },
		{ name: 'Laps', current: false },
	]

	function handleTabChange(idx: number) {
		tabs.forEach((t, i) => (tabs[i].current = false))
		tabs[idx].current = true
	}

	function classNames(...classes: string[]) {
		return classes.filter(Boolean).join(' ')
	}
</script>

<main>
	<section class="h-screen w-screen py-24 px-4">
		<div class="mx-auto max-w-[32rem]">
			<div>
				<!-- style="font-family: 'Racing Sans One'" -->
				<h1 class="mb-6 text-center text-4xl font-bold">Racing Fuel Calculator</h1>
				<nav class="isolate flex divide-x divide-gray-200 rounded-lg shadow" aria-label="Tabs">
					{#each tabs as tab, tabIdx}
						<button
							on:click={() => handleTabChange(tabIdx)}
							class={classNames(
								tab.current ? 'text-gray-900' : 'text-gray-500 hover:text-gray-700',
								tabIdx === 0 ? 'rounded-tl-lg' : '',
								tabIdx === tabs.length - 1 ? 'rounded-tr-lg' : '',
								'group relative min-w-0 flex-1 overflow-hidden bg-white py-4 px-4 text-center text-sm font-medium hover:bg-gray-50 focus:z-10'
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
			</div>
			<div class="flex flex-col space-y-6 bg-gray-50 p-8">
				<div class="w-full">
					<label class="mb-1 block" for="race-length"
						>Race Length <span class="float-right text-xs">in minutes</span></label>
					<input
						type="number"
						name="race-length"
						id="race-length"
						class="block w-full min-w-0 flex-1 rounded-md border-0 py-1.5 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
						placeholder="Race Length"
						bind:value={raceLengthMinutes} />
				</div>
				<div class="w-full">
					<label for="fuel-per-lap" class="mb-1 block">Fuel per Lap</label>
					<input
						type="number"
						name="fuel-per-lap"
						id="fuel-per-lap"
						class="block w-full min-w-0 flex-1 rounded-md border-0 py-1.5 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
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
							class="block w-full min-w-0 flex-1 rounded-none rounded-md rounded-l-md border-0 border-r-0 py-1.5 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
							placeholder="minutes"
							min="0"
							bind:value={minutes} />

						<span
							class="inline-flex items-center border border-r-0 border-gray-300 px-1 text-gray-500 sm:text-sm"
							>:</span>
						<input
							type="number"
							name="lap-time-seconds"
							id="lap-time-seconds"
							class="block w-full min-w-0 flex-1 rounded-none rounded-md border-0 py-1.5 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
							placeholder="seconds"
							min="0"
							max="59"
							bind:value={seconds} />

						<span
							class="inline-flex items-center border border-r-0 border-gray-300 px-1 text-gray-500 sm:text-sm"
							>:</span>
						<input
							type="number"
							name="lap-time-milliseconds"
							id="lap-time-milliseconds"
							class="block w-full min-w-0 flex-1 rounded-none rounded-md rounded-r-md border-0 py-1.5 px-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
							placeholder="milliseconds"
							min="0"
							max="999"
							bind:value={milliseconds} />
					</div>
				</div>
			</div>
			<div
				class="flex flex-col flex-wrap justify-between justify-between gap-y-4 rounded-b-lg border-2 border-indigo-500 bg-indigo-50 p-8 text-indigo-700 sm:flex-row">
				<div class="grow basis-1/2">
					<p class="text-2xl">{recommendedFuel}l</p>
					<p class="text-sm">Recommended Fuel</p>
				</div>
				<div class="grow basis-1/2">
					<p class="text-2xl">{safeFuel}l</p>
					<p class="text-sm">Safe Fuel (Full Formation Lap)</p>
				</div>
				<div class="grow basis-1/2">
					<p class="text-2xl">{minimumFuel}l</p>
					<p class="text-sm">Minimum Fuel</p>
				</div>
				<div class="grow basis-1/2">
					<p class="text-2xl">{laps}</p>
					<p class="text-sm">Total Laps</p>
				</div>
			</div>
		</div>
	</section>
</main>
