<script>
	import Footer from './footer.svelte' 
	import Nfts from './nfts.svelte'

	import {location, querystring} from 'svelte-spa-router'

	let valorIdToken = ' '
	let objetoTokenURL = {}

	let metadata = ''
	let objetoMetadata = {}

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

					metadata = consulta.map(token => toUtf8String(token.additionalRegisters.R5).substr(3))

					// Si trae metadata
					if(isJson(metadata)){
						objetoTokenURL = {
							id: consulta.map(token => token.assets[0].tokenId),
							name: consulta.map(token => token.assets[0].name),
							ch: consulta.map(token => token.creationHeight),
							description: '',
							r8: consulta.map(token => token.additionalRegisters.R8),
							r9: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
							r5: consulta.map(token => toUtf8String(token.additionalRegisters.R5).substr(2)),
							ext: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4))
						}
						objetoMetadata = JSON.parse(metadata)
						visualizoMetadata(objetoMetadata)
					
					// No tiene metadata
					} else {
						objetoTokenURL = {
							id: consulta.map(token => token.assets[0].tokenId),
							name: consulta.map(token => token.assets[0].name),
							ch: consulta.map(token => token.creationHeight),
							description: consulta.map(token => toUtf8String(token.additionalRegisters.R5).substr(2)),
							r8: consulta.map(token => token.additionalRegisters.R8),
							r9: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
							r5: consulta.map(token => toUtf8String(token.additionalRegisters.R5).substr(2)),
							ext: consulta.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4))
						}
					} 


				})
				.catch(error => console.error(error));
	}

	function isJson(str) {
		try {
			JSON.parse(str);
		} catch (e) {
			return false;
		}
		return true;
	}

	function visualizoMetadata(obj){
		for(var key in obj){
			if(!obj.hasOwnProperty(key)) continue;
				if(typeof obj[key] !== 'object') {
					if(key == 'image'){
						objetoTokenURL.description = objetoTokenURL.description + '<strong>' + letraMayuscula(key) + '</strong>: ' + '<a href="' + obj[key] + '">' + obj[key].substring(8, 30) + '...</a><br>'
					}else if(!Array.isArray(obj)){
						objetoTokenURL.description = objetoTokenURL.description + '<strong>' + letraMayuscula(key) + '</strong>: ' + obj[key] + '<br>'
					}
				} else {
					visualizoMetadata(obj[key])
				}
				if (Array.isArray(obj[key])){
					objetoTokenURL.description = objetoTokenURL.description + '<strong> ' + letraMayuscula(key) + ': </strong>' + obj[key] + '<br>'
				}
		}
	}

	function letraMayuscula(texto) {
		return texto.charAt(0).toUpperCase() + texto.slice(1);
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
						<div><span class="small text-break"><strong>Token ID: </strong><span class="text-secondary"> {objetoTokenURL.id}</span></span></div>
						<div><span class="small text-break"><strong>Artwork Checksum: </strong><span class="text-secondary"> {objetoTokenURL.r8}</span></span></div>
						<div><span class="small text-break"><strong>Name: </strong><span class="text-secondary"> {objetoTokenURL.name}</span></span></div>
						<div><span class="small text-break"><strong>Antiquity: </strong> <span class="text-secondary">{objetoTokenURL.ch}</span></span></div>
						<div class="accordion accordion-flush" id="accordionFlushExample">
							<div class="accordion-item">
							  <h2 class="accordion-header" id="flush-headingOne">
								<button class="btn btn-sm border border-secondary text-wrap" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseOne" aria-expanded="false" aria-controls="flush-collapseOne">
								  Description
								</button>
							  </h2>
							  <div id="flush-collapseOne" class="accordion-collapse collapse" aria-labelledby="flush-headingOne" data-bs-parent="#accordionFlushExample">
								<div class="accordion-body"><span class="small py-2 text-break"><br>{@html decodeURIComponent(objetoTokenURL.description)}</span></div>
							  </div>
							</div>
						</div>
					</div>
				</div>
				</div>
			</div> 
	{:else}
		<Nfts />
	{/if}
	<Footer />
</main>