<script lang="ts">
	import { applyAction, enhance } from '$app/forms';
	import { Result } from 'postcss';
	import type { ActionData, PageData } from './$types';

	export let form: ActionData;
	export let data: PageData;

	$: if (form?.message) alert(form.message);
	let { pl, py, pm } = data.total ?? { pl: 0, py: 0, pm: 0 };
	let loading = false;
	let success = false;
</script>

<div class="flex justify-center p-10">
	{#if data.loggedIn}
		<div class="bg-stone-700/50 p-10 flex flex-col gap-5 rounded">
			<table>
				<tr>
					<th>Pisteet</th>
					<th>Nimi</th>
				</tr>
				{#each data.top as entry}
					<tr>
						<td>{entry.score}</td>
						<td>{entry.value}</td>
					</tr>
				{/each}
			</table>

			<table>
				<tr>
					<td>{data.myscore}</td>
					<td>{data.email}</td>
				</tr>
			</table>

			<h2 class="text-center text-xl mt-3 -mb-4">Yhteensä</h2>
			<div class="flex justify-around items-end border-y">
				<div style="height: {(pl / Math.max(pl, py, pm)) * 100}px;" class="bg-green-400/90 w-10" />
				<div style="height: {(py / Math.max(pl, py, pm)) * 100}px;" class="bg-red-400/90 w-10" />
				<div style="height: {(pm / Math.max(pl, py, pm)) * 100}px;" class="bg-blue-400/90 w-10" />
			</div>
			<div class="flex justify-around -mt-2">
				<div class:font-bold={data.linja === 'lulu'} class="text-green-200/90 w-10">LULU</div>
				<div class:font-bold={data.linja === 'yle'} class="text-red-200/90 w-10">YLE</div>
				<div class:font-bold={data.linja === 'melu'} class="text-blue-200/90 w-10">MELU</div>
			</div>
		</div>
	{:else if form?.success}
		<div class="bg-stone-700/50 p-5 text-xl rounded ">
			Katso sähköpostisi (myös roskaposti). Voit sulkea tämän välilehden.
		</div>
	{:else if loading}
		<div class="bg-stone-700/50 p-5 text-xl rounded ">...</div>
	{:else}
		<form
			class="bg-stone-700/50 flex flex-col gap-2 rounded p-5"
			method="POST"
			use:enhance={() => {
				loading = true;
				return async ({ result, update }) => {
					await update();
					loading = false;
				};
			}}
		>
			<label for="email" class="text-xl">Sähköposti:</label>
			<input
				placeholder="xd1234@edu.turku.fi"
				type="email"
				name="email"
				class="text-black p-2 rounded"
			/>
			<div class="flex justify-around">
				<div>
					<input type="radio" id="lulu" name="linja" value="lulu" />
					<label for="lulu">LULU</label>
				</div>
				<div>
					<input type="radio" id="yle" name="linja" value="yle" />
					<label for="yle">YLE</label>
				</div>
				<div>
					<input type="radio" id="melu" name="linja" value="melu" />
					<label for="melu">MELU</label>
				</div>
			</div>

			<input
				type="submit"
				class="bg-stone-700/50 p-2 rounded cursor-pointer hover:bg-stone-700/80"
				value="Kirjaudu"
			/>
		</form>
	{/if}
</div>

<style>
	table tr td,
	table tr th {
		padding: 10px;
	}
	table {
		border: white solid 1px;
		border-width: 1px;
		padding: 5px;
	}
</style>
