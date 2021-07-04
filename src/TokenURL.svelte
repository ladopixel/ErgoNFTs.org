<script>let valorIdToken = ' '
	let objetoTokenURL = {}

	// Rescatar valor y mostrar token desde URL
	// http://localhost:5000/#/?token=9a9cac59e2266b5e4325ba4eda8487ac8d76879dc6e5c8294e6784cb345ee7a2
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
	}</script>

<main>
	<!-- Modal muestraTokenURL -->
	<button id="buttonModalTokenURL" type="button" class="btn btn-dark" data-bs-toggle="modal" data-bs-target="#modalTokenURL">Ver token</button> 
	<div class="modal fade" id="modalTokenURL" tabindex="-1" aria-labelledby="exampleModalLabelToken" aria-hidden="true">
		<div class="modal-dialog modal-lg">
		<div class="modal-content">
			<div class="modal-header bg-secondary">
			<h5 class="modal-title bg-dark text-light py-1 px-3" id="exampleModalLabelToken">ergonfts.org</h5>
			<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
			</div>
			<div class="modal-body">
				<div><span class="small"><strong>Token ID: </strong> {objetoTokenURL.id}</span></div>
				<div><span class="small"><strong>Name: </strong> {objetoTokenURL.name}</span></div>
				<div><span class="small"><strong>Antiquity: </strong> {objetoTokenURL.ch}</span></div>
				{#if (objetoTokenURL.r9 != '')}
					<img src={objetoTokenURL.r9} class="card-img-top mb-3 imageBorder" alt={objetoTokenURL.name} width="100">
				{/if}
			</div>
		</div>
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