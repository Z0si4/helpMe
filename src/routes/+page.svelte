<script lang="ts">
	import { onMount } from 'svelte';
	import { AppShell } from '@skeletonlabs/skeleton';

	import { addDoc, collection, GeoPoint } from 'firebase/firestore';
	import { db } from '$lib/db';
	let helpCount = 0;

	onMount(() => {
		// Pobierz wartość z ciasteczka, jeśli istnieje
		const helpCountCookie = parseInt(getCookie('helpCount'), 10);
		if (!isNaN(helpCountCookie)) {
			helpCount = helpCountCookie;
		}
	});

	function getCookie(name: string) {
		const value = `; ${document.cookie}`;
		const parts = value.split(`; ${name}=`);
		if (parts.length === 2) return parts.pop().split(';').shift();
	}

	function setCookie(name: string, value: string, days: number) {
		const expires = new Date();
		expires.setTime(expires.getTime() + days * 24 * 60 * 60 * 1000);
		document.cookie = `${name}=${value};expires=${expires.toUTCString()};path=/`;
	}
	let n: string;
	function handleClick() {
		// Zwiększ licznik
		helpCount++;

		// Ustaw nową wartość ciasteczka
		setCookie('helpCount', helpCount.toString(), 365);
		if (!navigator.geolocation) {
			alert('er');
			return;
		}
		const col = collection(db, '1');
		const loc = navigator.geolocation.getCurrentPosition(async (e) => {
			await addDoc(col, { cords: new GeoPoint(e.coords.latitude, e.coords.longitude), name: n });
		},
    f=>{
      alert("err")
    });
	}
</script>

<div class="bg-primary-100">
	<AppShell>
		<svelte:fragment slot="sidebarLeft">
			<div
				class="card p-10 pb-60 pt-60 block justify-center items-center bg-surface-600 mt-5 text-center rounded-xl"
			>
				<p class="text-white variant-ghost-primary p-6 font-extrabold" id="helpCounter ">
					Liczba ludzi, którym już pomogliśmy:<br /><br />
					<span class="text-6xl font-serif text-center">{helpCount}</span>
				</p>
				<hr class="!border-t-2" />
			</div>
		</svelte:fragment>
		<svelte:fragment slot="sidebarRight">
			<div class="card p-10 pb-40 pt-40 block justify-center items-center bg-surface-300 mt-10">
				<button type="button" class="btn variant-filled-error p-10" on:click={handleClick}>
					WEZWIJ POMOC!!!
				</button>
				<input type="text" bind:value={n} />
			</div>
		</svelte:fragment>
		<div class="flex justify-content w-full h-full">
			<img class="justify-center" src="icon-rectangle-NoBg.png" alt="logo" />
		</div>
		<svelte:fragment slot="footer">
			<div
				class="card text-white p-10 block justify-center items-center bg-surface-400 mt-5"
				id="left"
				style="text-align:center;"
			>
				<a href="/submitions"
					><button type="button" class="btn variant-filled rounded-full w-80 h-20"
						>Zostan wolontariuszem</button
					></a
				>
			</div>
		</svelte:fragment>
	</AppShell>
</div>
