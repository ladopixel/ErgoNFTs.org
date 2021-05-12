<script>
    let arrayIds = []
    let objetoIdDesc = {}
	
	let arrayDatos = []
    let objeto = {}

    let urlDescription = ''

    let arrayFavorites = []
	let infoFavorite = {id: '', status: ''}

    const listadosTimeLine = async() => {
        arrayDatos = []
        arrayIds = []
        try {
            const res = await fetch(`https://api.ergoplatform.com/api/v1/tokens?limit=100`)
            const data = await res.json()

            objetoIdDesc = {
                    id: data.items.map(token => token.id),
                    desc: data.items.map(token => token.description || 'No description')
                }

            arrayIds = objetoIdDesc

                for(let i = 0; i < data.items.length; i++){
                    const res2 = await fetch(`https://api.ergoplatform.com/api/v0/assets/${arrayIds.id[i]}/issuingBox`)
                    const data2 = await res2.json()
                    let R9info = data2.map(token => token.additionalRegisters.R9)
                    linkify(arrayIds.desc[i])
                    if(R9info != '' ){
                        objeto = {
                            id: data2.map(token => token.assets[0].tokenId),
                            name: data2.map(token => token.assets[0].name),
                            r9: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2)),
                            ext: data2.map(token => toUtf8String(token.additionalRegisters.R9).substr(2).slice(-4))
                        }
                    } else {
                        objeto = {
                            id: data2.map(token => token.assets[0].tokenId),
                            name: data2.map(token => token.assets[0].name),
                            r9: urlDescription,
                            ext: urlDescription.slice(-4)
                        }
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

var urlRegex =/(\b(https?|ftp|file):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/ig;
function linkify(text) {
    return text.replace(urlRegex, function(url) {
        urlDescription = url
        return url;
    });
}

listados()
</script>

<svelte:head>
	<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
	<script src="popper.min.js"></script>
	<script src="bootstrap.js"></script>
	<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
	<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js" integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf" crossorigin="anonymous"></script>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.1/font/bootstrap-icons.css">
</svelte:head>

<main>
    <div class="my-2 bg-light pb-1">
		<div class="bg-secondary py-3 px-5 text-light border-bottom border-dark">
			<i class="bi bi-wind"></i>
			<span>The last NFTs</span>
		</div>
		<div class="row mx-2 my-2">
			{#await listados}
				<p class="text-secondary">Loading...</p>
			{:then listados}
				{#each arrayDatos as datos}
					{#if datos.ext == '.png' || datos.ext == '.gif' || datos.ext == '.jpg' || datos.ext == 'jpeg' || datos.ext == '.bmp' || datos.ext == '.svg' || datos.ext == '.raf' || datos.ext == '.nef'}
						<div class="card mt-2 mx-1 cardColor">
							<div>
								{#if datos.class == true}
									<button class="btn text-info" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>	
								{:else}
									<button class="btn text-light" on:click={addFavorite(datos.id)}><i class="bi bi-heart-fill " title="Add favorite"></i></button>
								{/if}
							</div>
							<a href={datos.r9} target="_blank" title={datos.name}>
								<img src={datos.r9} class="card-img-top mb-3 imageBorder" alt={datos.name} width="200">
							</a>
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
							<a href={datos.r9} target="_blank" title={datos.name}>
								<audio src={datos.r9} class="card-img-top mb-3 " title={datos.name} controls></audio> 
							</a>
						</div>
					{/if}
				{/each}
			{:catch listados}
				<p>Something went wrong: {error.message}</p>
			{/await}
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