<script>
	import Footer from './footer.svelte' 
	import Nfts from './nfts.svelte'

	import {location, querystring} from 'svelte-spa-router'

	let valorIdToken = ' '
	let objetoTokenURL = {}

	// Rescatar valor y mostrar token desde URL
	valorIdToken = JSON.stringify($querystring)
	if (valorIdToken.substring(1, 6) == 'token'){
		valorIdToken = valorIdToken.substring(7, valorIdToken.length - 1);
		muestraTokenURL (valorIdToken)
	}

	function muestraTokenURL(valorIdToken) {
			fetch(`https://api.ergoplatform.com/api/v0/assets/${valorIdToken}/issuingBox`)
				.then(response => response.json())
				.then(consulta => {
					objetoTokenURL = {
						id: consulta.map(token => token.assets[0].tokenId),
						name: consulta.map(token => token.assets[0].name),
						ch: consulta.map(token => token.creationHeight),
						r9: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
						r5: consulta.map(token => toUtf8String(token.additionalRegisters.R5).substr(2)),
						ext: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4))
					}
				})
				.catch(error => console.error(error));
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

	function cerrarModal(){
		window.location.href = 'https://ergonfts.org'
	}
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

<main>
	{#if (objetoTokenURL.id)}
		<br>
			<div id="modalTokenURL" tabindex="-1" aria-labelledby="exampleModalLabelToken" aria-hidden="true">
				<div class="modal-dialog modal-lg">
				<div class="modal-content">
					<div class="modal-header bg-secondary">
						<a href="https://ergonfts.org" class=" mx-3 separaI"><img src="Ergo-black.png" alt="Logotype Ergo" width="100"></a>
						<button on:click|once={cerrarModal} type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
					</div>
					<div class="modal-body">
						{#if (objetoTokenURL.r9 != '')}
							<img src={objetoTokenURL.r9} class="card-img-top mb-3 imageBorder"  alt={objetoTokenURL.name} >
						{/if}
						<hr>
						<div><span class="small"><strong>Token ID: </strong> {objetoTokenURL.id}</span></div>
						<div><span class="small"><strong>Name: </strong> {objetoTokenURL.name}</span></div>
						<div><span class="small"><strong>Antiquity: </strong> {objetoTokenURL.ch}</span></div>
					</div>
				</div>
				</div>
			</div> 
	{:else}
		<Nfts />
	{/if}
	<Footer />
</main>