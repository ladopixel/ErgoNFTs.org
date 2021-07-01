<script >
	import {parse} from 'qs'
	import {location, querystring} from 'svelte-spa-router'
	
	let valorWallet = ' '
	let valorIdToken = ' '
	let claseGuardarLocalstorage = ''
	const onFocus = () => valorWallet = '';

	let arrayIds = []
	let objeto = {}
	let arrayDatos = []

	let arrayDatosTimeLine = []
	let arrayIdsTimeLine = []
	let objetoTimeLine = {}
	let whale = ''

	let offsetTokens = 0
	let limitTokens = 20
	let limitDisabled = false
	let offsetDisabled = false

	let arrayWallets = []
	let infoWallet = {id: '', address: ''}
	let selected = ''

	let arrayFavorites = []
	let infoFavorite = {id: '', status: ''}

	let objetoIdDesc = {}
	let urlDescription = ''

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
				for (let i = 0; i < arrayDatosTimeLine.length; i++){
					if (idNft[0] == arrayDatosTimeLine[i].id){
						arrayDatosTimeLine[i].class = false
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
			for (let i = 0; i < arrayDatosTimeLine.length; i++){
					if (idNft[0] == arrayDatosTimeLine[i].id){
						arrayDatosTimeLine[i].class = true
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
			if (arrayIds.length <= 0){
				alert('No tokens')
			}
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

	const sumaOffset = () => {
		offsetTokens = parseInt(offsetTokens) + parseInt(limitTokens)
		listadosTimeLine()
	}
	const inicioOffset = () => {
		offsetTokens = 0
		listadosTimeLine()
	}

	const listadosTimeLine = async() => {
		limitDisabled = true
		offsetDisabled = true
        arrayDatosTimeLine = []
        arrayIdsTimeLine = []
        try {

			const res = await fetch(`https://api.ergoplatform.com/api/v1/tokens?offset=${offsetTokens}&limit=${limitTokens}`)
			const data = await res.json()
			arrayIdsTimeLine = data.items.filter(token => token.emissionAmount <= 1)
			if (arrayIdsTimeLine.length < (limitTokens - 1)) {
				whale = 'If you see that it takes a while, wait a few seconds, be patient. You are going through the thousands of tokens created in the time of the whale...'
				do {
					offsetTokens = parseInt(offsetTokens) + parseInt(limitTokens)
					const res = await fetch(`https://api.ergoplatform.com/api/v1/tokens?offset=${offsetTokens}&limit=${limitTokens}`)
					const data = await res.json()
					arrayIdsTimeLine = arrayIdsTimeLine.concat(data.items.filter(token => token.emissionAmount <= 1))
				} while (arrayIdsTimeLine.length < limitTokens)
			}
			whale = ''

            for(let i = 0; i < arrayIdsTimeLine.length; i++){
                const res2 = await fetch(`https://api.ergoplatform.com/api/v0/assets/${arrayIdsTimeLine[i].id}/issuingBox`)
                const data2 = await res2.json()
				
				let R7info = data2.map(token => token.additionalRegisters.R7)
				if (R7info == '0e020101' || R7info == '0e020102'){
					let R9info = data2.map(token => token.additionalRegisters.R9)
					let R5info = data2.map(token => token.additionalRegisters.R5)
					if (R9info != '') {
						linkify(arrayIdsTimeLine[i].description)
						objetoTimeLine = {
							id: data2.map(token => token.assets[0].tokenId),
							name: data2.map(token => token.assets[0].name),
							description: data2.map(token => toUtf8String(token.additionalRegisters.R5).substr(2)),
							r9: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
							ext: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4)),
							urlInTxt: urlDescription
						}
					} else if ((R9info == '') && (arrayIdsTimeLine[i].description != '')) {
							linkify(arrayIdsTimeLine[i].description)
							objetoTimeLine = {
								id: data2.map(token => token.assets[0].tokenId),
								name: data2.map(token => token.assets[0].name),
								description: data2.map(token => toUtf8String(token.additionalRegisters.R5)),
								r9: urlDescription,
								ext: urlDescription.slice(-4),
								urlInTxt: urlDescription
							}
					}
				}
				arrayDatosTimeLine[i] = objetoTimeLine
				urlDescription = ''
				objetoTimeLine = {id: '', name: '', description: '', r9: '', ext: '', urlInTxt: '' }
			}
			for(let j = 0; j < arrayDatosTimeLine.length; j++){
                for (let k = 0; k < arrayFavorites.length; k++){
                      if (arrayDatosTimeLine[j].id == arrayFavorites[k].id){
                	    arrayDatosTimeLine[j].class = true
                    }
                }
            }
			limitDisabled = false
			offsetDisabled = false
        } catch (error) {
            console.log(error)
        }
	}
	
var urlRegex =/(\b(https?|ftp|file|ipfs):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/ig;
function linkify(text) {
    return text.replace(urlRegex, function(url) {
		urlDescription = url
        return url;
    });
}


if (JSON.stringify($location).substr(2)){
	valorWallet = JSON.stringify($location).substr(2)
	valorWallet = valorWallet.substring(0, valorWallet.length - 1);
	listados()
}

valorIdToken = JSON.stringify($querystring)
if (valorIdToken.substring(1, 6) == 'token'){
	// http://localhost:5000/#/?token=9a9cac59e2266b5e4325ba4eda8487ac8d76879dc6e5c8294e6784cb345ee7a2
	valorIdToken = valorIdToken.substring(7, valorIdToken.length - 1);
	//muestraTokenURL (valorIdToken)
	alert(valorIdToken)
}




listadosTimeLine ()
</script>

<svelte:head>
	<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
	<script src="popper.min.js"></script>
	<script src="bootstrap.js"></script>
	<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
	<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js" integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf" crossorigin="anonymous"></script>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.1/font/bootstrap-icons.css">
	<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/1.3.2/jspdf.min.js"></script>
</svelte:head>

<!-- Cabecera -->
<main class=" bg-dark">
	<nav class="navbar navbar-expand-md navbar-dark bg-dark fixed-top">
		<a href="https://ergonfts.org" class="mb-1 mx-3 separaI"><img src="ergo.png" alt="Logotype Ergo" width="100"></a>
		<button class="navbar-toggler mx-3" type="button" data-toggle="collapse" data-target="#navbar" aria-controls="navbar" aria-expanded="false" aria-label="Toggle navigation">
			<span class="navbar-toggler-icon"></span>
		</button>
		<div class="collapse navbar-collapse interiorT" id="navbar">
			<a href="https://ergotokens.org" title="Ergo Tokens" class="anchoT btn bg-dark text-secondary border border-secondary mx-2">Tokens</a>
			<div class="input-group bg-dark">
				<button on:click={listFavoritePicture} title="List of favorites" class="input-group-text dropdown border border-dark bg-light text-info" id="basic-addon1"><i class="bi bi-heart-fill"></i></button>
				<button on:click={cleanLocalStorage} title="Clean localstorage" class="input-group-text dropdown border border-dark bg-light {claseGuardarLocalstorage}" id="basic-addon1"><i class="bi bi-door-open-fill"></i></button>
				<input on:focus={onFocus} bind:value={valorWallet} class="form-control border border-dark" placeholder="Your wallet"  aria-label="Your wallet" aria-describedby="basic-addon1">
				<button on:click={guardarLocalstorage} title="Add wallet" class="input-group-text dropdown border border-dark bg-light {claseGuardarLocalstorage}" id="basic-addon1"><i class="bi bi-wallet-fill"></i></button>
			</div>
			<button on:click={listados} class="anchoT btn border-secondary border-seconda text-light mx-2 my-2 " title="Accept">Accept</button>
			<!-- Wallet -->
			<select bind:value={selected} class="nav-link bg-dark border border-secondary text-light mx-2 rounded">
				<option value=0 class="dropdown-item" selected>Your Wallets</option>
				{#each arrayWallets as wallet}
					<option value={wallet.address} class="dropdown-item form">{wallet.address.substring(0, 7)}...{wallet.address.slice(-7)}</option>
				{/each}
			</select>
		</div>
	  </nav>

	  	<br><br><br>

	<!--  -->
	<div class="my-2 bg-light pb-1">
		<div class="bg-secondary py-3 px-5 text-light border-bottom border-dark">
			<i class="bi bi-music-note-list"></i>
			<span>Your Audio NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#await listados}
				<p class="text-secondary">Loading...</p>
			{:then listados}
				{#each arrayDatos as datos}
					{#if datos.ext == '.mp3' || datos.ext == '.ogg' || datos.ext == '.wma' || datos.ext == '.wav' || datos.ext == '.aac' || datos.ext == 'aiff'}
						<div class="card mt-2 mx-1 cardColor">
							<div>
								{#if datos.class == true}
									<button class="btn text-info" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
								{:else}
									<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
								{/if}
								{datos.name}
							</div>
							<audio src={datos.r9} class="card-img-top mb-3 " title={datos.name} controls></audio> 
						</div>
					{/if}
				{/each}
			{:catch error}
				<p class="text-secondary">Something went wrong: {error.message}</p>
			{/await}
		</div>
	</div>

	<!-- Imágenes -->
	<div class="my-2 bg-light pb-1">
		<div class="bg-secondary py-3 px-5 text-light border-bottom border-dark">
			<i class="bi bi-images"></i>
			<span>Your Picture NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#await listados}
				<p class="text-secondary">Loading...</p>
			{:then listados}
				{#each arrayDatos as datos}
					{#if datos.ext == '.png' || datos.ext == '.gif' || datos.ext == '.jpg' || datos.ext == 'jpeg' || datos.ext == '.bmp' || datos.ext == '.svg' || datos.ext == '.raf' || datos.ext == '.nef'}
						<div class="card mt-2 mx-1 cardColor">
							<a href="https://twitter.com/intent/tweet?text=Enjoy%20this%20NFT%20created%20in%20@ergoplatformorg&url=https://ergonfts.org/%23/?token={datos.id}&hashtags=ErgoNFTs,NFTart,Ergo2Top10" target="_blank">Twitter</a>
							<div>
								{#if datos.class == true}
									<button class="btn text-info" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
								{:else}
									<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
								{/if}
							</div>
							<a href={datos.r9} title={datos.name} data-bs-toggle="modal" data-bs-target="#modalToken{datos.id}">
								<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="200">
							</a>
							<!-- Modal -->
							<div class="modal fade " id="modalToken{datos.id}" tabindex="-1" aria-labelledby="exampleModalLabelToken" aria-hidden="true">
								<div class="modal-dialog modal-lg">
								<div class="modal-content">
									<div class="modal-header bg-secondary">
									<h5 class="modal-title bg-dark text-light py-1 px-3" id="exampleModalLabelToken">ergonfts.org</h5>
									<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
									</div>
									<div class="modal-body">
										<div><span class="small"><strong>Token ID: </strong> {datos.id}</span></div>
										<div><span class="small"><strong>Name: </strong> {datos.name}</span></div>
										<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="100">
									</div>
								</div>
								</div>
							</div>	
						</div>
					{/if}
				{/each}
			{:catch listados}
				<p>Error</p>
			{/await}
		</div>
	</div>

	<div class="my-2 bg-light pb-1">
		<div class="bg-secondary py-3 px-5 text-light border-bottom border-dark">
			<i class="bi bi-wind"></i>
			<span>The last NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#await listadosTimeLine}
				<p class="text-secondary">Loading...</p>
			{:then listadosTimeLine}
				{#each arrayDatosTimeLine as datos}
					{#if datos.ext == 'ink/' || datos.ext == '.png' || datos.ext == '.gif' || datos.ext == '.jpg' || datos.ext == 'jpeg' || datos.ext == '.bmp' || datos.ext == '.svg' || datos.ext == '.raf' || datos.ext == '.nef'}
					<div class="card mt-2 mx-1 cardColor">
						<a href="https://twitter.com/intent/tweet?text=Enjoy%20this%20NFT%20created%20in%20@ergoplatformorg&url=https://ergonfts.org/%23/?token={datos.id}&hashtags=ErgoNFTs,NFTart,Ergo2Top10" target="_blank">Twitter</a>
							<div>
								{#if datos.class == true}
									<button class="btn text-info" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
								{:else}
									<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
								{/if}
							</div>
							<a href={datos.r9} title={datos.name} data-bs-toggle="modal" data-bs-target="#modalToken{datos.id}">
								<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="200">
							</a>
							<!-- Modal -->
							<div class="modal fade " id="modalToken{datos.id}" tabindex="-1" aria-labelledby="exampleModalLabelToken" aria-hidden="true">
								<div class="modal-dialog modal-lg">
								<div class="modal-content">
									<div class="modal-header bg-secondary">
									<h5 class="modal-title bg-dark text-light py-1 px-3" id="exampleModalLabelToken">ergonfts.org</h5>
									<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
									</div>
									<div class="modal-body">
										<div><span class="small"><strong>Token ID: </strong> {datos.id}</span></div>
										<div><span class="small"><strong>Name: </strong> {datos.name}</span></div>
										<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="100">
									</div>
								</div>
								</div>
							</div>	
							<span class="h6">{datos.name}</span><span class="small mb-2 text-secondary">{decodeURIComponent(escape(datos.description))}</span>
							{#if (datos.urlInTxt !='')}
								<hr class="text-secondary">
								<span class="mb-3 text-dark"><i class="bi bi-link"></i>
								<a href={datos.urlInTxt} class="text-dark small">{datos.urlInTxt.substring(8, 30)}...</a></span>
							{/if}
						</div>
					{/if}
                    {#if datos.ext == '.mp3' || datos.ext == '.ogg' || datos.ext == '.wma' || datos.ext == '.wav' || datos.ext == '.aac' || datos.ext == 'aiff'}
						<div class="card mt-2 mx-1 cardColor">
							<div>
								{#if datos.class == true}
									<button class="btn text-info" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
								{:else}
									<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
								{/if}
                                {datos.name}
							</div>
								<audio src={datos.r9} class="card-img-top mb-3 " title={datos.name} controls></audio> 
								<span class="h6">{datos.name}</span><span class="small mb-2 text-secondary">{decodeURIComponent(escape(datos.description))}</span>
								{#if (datos.urlInTxt !='')}
									<hr class="text-secondary">
									<span class="mb-3 text-dark"><i class="bi bi-link"></i>
									<a href={datos.urlInTxt} class="text-dark small">{datos.urlInTxt.substring(8, 30)}...</a>
									</span>
								{/if}
						</div>
					{/if}
				{/each}
			{:catch listadosTimeLine}
				<p>Error</p>
			{/await}
		</div>
		<div class="bg-secondary py-3 px-5 text-light border-bottom border-dark">
			<button on:click={sumaOffset} disabled={offsetDisabled} class="bg-secondary text-light border border-secondary"><i class="bi bi-arrow-right-square-fill" style="font-size: 2rem;"></i></button>
			<span class="small">{whale}</span>
		</div>
	</div>

</main>

<style>
	.cardColor{
		background-color: #cfd0d2;
		border: 0px;
        min-width: 18rem;
		width: 18rem; 
		min-width: 18rem;
	}
	.imageBorder{
		border: 5px solid #ffffff;
	}
	@media only screen and (max-width: 768px) {
		.cardColor{
			width: 97%;
		}	
		.anchoT {
			width: 97%;
			margin-bottom: 10px;
			margin-left: 0px;
		}
		.interiorT{
			padding: 10px;
		}
		.separaI{
			margin-top: 5px;;
		}
	}
</style>