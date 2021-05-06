<script>

import AudioPlayer, { stopAll } from './AudioPlayer.svelte';

	let valorWallet = ' '
	let claseGuardarLocalstorage = ''
	const onFocus = () => valorWallet = '';

	let arrayIds = []
	let objeto = {}
	let arrayDatos = []

	let arrayWallets = []
	let infoWallet = {id: '', address: ''}
	let selected = ''

	let arrayFavorites = []
	let infoFavorite = {id: '', status: ''}

	if (localStorage.getItem("arrayWallets")){
		arrayWallets = JSON.parse(localStorage.getItem("arrayWallets"))
	}
	$: localStorage.setItem("arrayWallets", JSON.stringify(arrayWallets))

	function guardarLocalstorage(){
		var mensaje = confirm(`Do you want to save this wallet in your browser?\n${valorWallet} \n\n This way you don't have to reintroduce it :)`);
		if (mensaje){
			infoWallet.id = Date.now()
			infoWallet.address = valorWallet
			arrayWallets = [...arrayWallets, infoWallet]
			infoWallet = {id: '', address: ''}
		}
	}

	if (localStorage.getItem("arrayFavorites")){
		arrayFavorites = JSON.parse(localStorage.getItem("arrayFavorites"))
	}
	$: localStorage.setItem("arrayFavorites", JSON.stringify(arrayFavorites))

	function addFavorite(idNft){
		let isFavorite = false
		for(let i=0; i < arrayFavorites.length; i++){
			if(idNft[0] == arrayFavorites[i].id){
				arrayFavorites = arrayFavorites.filter(item => item.id !== idNft[0])
				isFavorite = true
				for (let i = 0; i < arrayDatos.length; i++){
					if (idNft[0] == arrayDatos[i].id){
						arrayDatos[i].class = false
					}
				}
			}
		}
		if (isFavorite == false){
			infoFavorite.id = idNft[0]
			infoFavorite.status = true
			arrayFavorites = [...arrayFavorites, infoFavorite]
			infoFavorite = {id: '', status: false}
			for (let i = 0; i < arrayDatos.length; i++){
				if (idNft[0] == arrayDatos[i].id){
					arrayDatos[i].class = true
				}
			}
		}
	}

	$: {
		if ((selected != '') && (selected !=0)) {
			valorWallet = selected
			listados()
		}
	}

	const listados = async() => {
		arrayDatos = []
		try {
			const res = await fetch(`https://api.ergoplatform.com/api/v0/addresses/${valorWallet}`)
			const data = await res.json()
			const arrayIds = data.transactions.confirmedTokensBalance.map(token => token.tokenId)
				for(let i = 0; i < arrayIds.length; i++){
					const res2 = await fetch(`https://api.ergoplatform.com/api/v0/assets/${arrayIds[i]}/issuingBox`)
					const data2 = await res2.json()
					objeto = {
						id: data2.map(token => token.assets[0].tokenId),
						name: data2.map(token => token.assets[0].name),
						r9: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
						ext: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4)),
					}
					arrayDatos[i] = objeto
				}
				for(let j = 0; j < arrayDatos.length; j++){
					for (let k = 0; k < arrayFavorites.length; k++){
						if (arrayDatos[j].id == arrayFavorites[k].id){
							arrayDatos[j].class = true
						}
					}
				}
			
		} catch (error) {
			console.log(error)
		}
	}

 const listFavoritePicture = async() => {
	arrayDatos = []
		try {
			for(let i = 0; i < arrayFavorites.length; i++){
				const res2 = await fetch(`https://api.ergoplatform.com/api/v0/assets/${arrayFavorites[i].id}/issuingBox`)
				const data2 = await res2.json()
				objeto = {
					id: data2.map(token => token.assets[0].tokenId),
					name: data2.map(token => token.assets[0].name),
					r9: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
					ext: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4)),
					class: true
				}
				arrayDatos[i] = objeto
			}
		} catch (error) {
			console.log(error)
		}
	}

	const cleanLocalStorage = () => {
		localStorage.clear()
		location.reload()
	}

	function toUtf8String(hex) {
		if(!hex){
			hex = ''
		}
		var str = '';
		for (var i = 0; i < hex.length; i += 2) {
			str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
		}
		return str;
	}
	
</script>

<svelte:head>
	<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
	<script src="popper.min.js"></script>
	<script src="bootstrap.js"></script>
	<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
	<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js" integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf" crossorigin="anonymous"></script>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.1/font/bootstrap-icons.css">
</svelte:head>

<!-- Cabecera -->
<main class=" bg-dark">
	<div class="row g-3 bg-dark px-3 py-3">
		<div class="col-sm-3 col-md-3">
			<a href="https://ergotokens.org" class="mb-1"><img src="ergo.png" alt="Logotype Ergo" width="100"></a>
			<span class="mx-2"> </span>
			<!--<button class="btn bg-dark text-secondary border border-secondary">Tokens</button>-->
		</div>
		<div class="col-6 col-md-6">
			<div class="input-group mb-3 bg-dark">
				<button on:click={listFavoritePicture} title="List of favorites" class="input-group-text dropdown" id="basic-addon1"><i class="bi bi-heart-fill"></i></button>
				<button on:click={cleanLocalStorage} title="Clean localstorage" class="input-group-text dropdown {claseGuardarLocalstorage}" id="basic-addon1"><i class="bi bi-door-open-fill"></i></button>
				<input on:focus={onFocus} bind:value={valorWallet} class="form-control " placeholder="Your wallet"  aria-label="Your wallet" aria-describedby="basic-addon1">
				<button on:click={guardarLocalstorage} title="Add wallet" class="input-group-text dropdown {claseGuardarLocalstorage}" id="basic-addon1"><i class="bi bi-wallet-fill"></i></button>
			</div>
		</div>
		<div class="col-3 col-md-3">
			<div>
				<button on:click={listados} class="btn bg-dark text-light border border-secondary ml-2">Accept</button>
				<select bind:value={selected} class="btn btn-secondary bg-dark">
					<option value=0 class="dropdown-item" selected>Select wallet</option>
					{#each arrayWallets as wallet}
						<option value={wallet.address} class="dropdown-item">{wallet.address.substring(0, 7)}...{wallet.address.slice(-7)}</option>
					{/each}
				</select>
			</div>
		</div> 
	</div>	
	
	<!-- Audio -->
	<div class="mx-2 my-2 bg-light pb-1">
		<div class="bg-secondary py-2 px-3">
			<i class="bi bi-music-note-list"></i>
			<span>Your Audio NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#each arrayDatos as datos}
				{#if datos.ext == '.mp3' || datos.ext == '.ogg' || datos.ext == '.wma' || datos.ext == '.wav' || datos.ext == '.aac' || datos.ext == 'aiff'}
					<div class="card mt-2 mx-1 cardColor" style="width: 18rem;">
						<div>
							{#if datos.class == true}
								<button class="btn text-danger" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
							{:else}
								<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
							{/if}
						</div>
						<AudioPlayer
							src={datos.r9}
							title={datos.name}	
						/>
						<!-- <audio src={datos.r9} class="card-img-top mb-3 bg-light" title={datos.name} controls></audio> -->
					</div>
				{/if}
			{/each}
		</div>
	</div> 
	
	<!-- Picture -->
	<div class="mx-2 my-2 bg-light pb-1">
		<div class="bg-secondary py-2 px-3">
			<i class="bi bi-images"></i>
			<span>Your Picture NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#each arrayDatos as datos}
				{#if datos.ext == '.png' || datos.ext == '.gif' || datos.ext == '.jpg' || datos.ext == 'jpeg' || datos.ext == '.bmp' || datos.ext == '.svg' || datos.ext == '.raf' || datos.ext == '.nef'}
					<div class="card mt-2 mx-1 cardColor" style="width: 18rem;">
						<div>
							{#if datos.class == true}
								<button class="btn text-danger" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
							{:else}
								<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
							{/if}
						</div>
						<a href={datos.r9} target="_blank" title={datos.name}>
							<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="200">
						</a>
					</div>
				{/if}
			{/each}
		</div>
	</div>
</main>

<style>
	.cardColor{
		background-color: rgb(190, 190, 190);
		border: 0px;
	}
	.imageBorder{
		border: 5px solid #ffffff;
	}
</style>