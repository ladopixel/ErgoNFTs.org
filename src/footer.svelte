<script>
	let imageDonation = 'QR-donate-9ggm3WB6e2BMbbCD3C9PJcgm5wbvmvCUG7w5rgszbKVWrVr8fSE.png'
	let structureMetadata = '{\n    "721": {\n        "ergnomes-1": {\n            "image": "https://ipfs.io/ipfs/QmakvdhLeydDSPY9A4L4wCWjDbNhKLMLcejJypCUvVH8fG/1_Amateur-supergnome1.png",\n            "index": 1,\n            "series": 1,\n            "name": "Amateur Superergnome",\n            "description": "Your everyday superhero. Dont let your fears dwarf you: if you can dream it, you can Supergnome it.",\n            "type": "ERGnome",\n            "sex": "Male",\n            "rarity": "Common",\n            "tribe": "Villager",\n            "traits": [\n                "Thornyhero",\n                "Thornyhero",\n                "Thornyhero"\n            ]\n        }\n    }\n}'
</script>
<center>
	<small>
		<br>
		<button type="button" title="FAQ" class="btn btn-dark" data-bs-toggle="modal" data-bs-target="#modalFaq"> FAQ</button>
		<a href="https://ergoplatform.org" title="ergoplatform.org" class="text-secondary">
			<img src="Ergo-miniatura.png" title="ergoplatform.org" width="25" class="my-2 mx-2" alt="Logotype Ergo">ergoplatform.org
		</a>
		<a href="https://github.com/ladopixel/ergonfts" title="ergonfts.org Github" class="text-secondary">
			<img src="Github.png" title="ergonfts.org Github " width="25" class="my-2 mx-2" alt="Logotype Github">Github
		</a>
		<button type="button" title="Donate :)" class="btn btn-dark" data-bs-toggle="modal" data-bs-target="#modalDonate"> <i class="bi bi-hand-thumbs-up-fill"></i></button>
	</small>
</center>
	<!-- Modal -->
	<div class="modal fade " id="modalDonate" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
		<div class="modal-dialog modal-lg">
	  		<div class="modal-content">
				<div class="modal-header bg-secondary">
		  			<h5 class="modal-title bg-dark text-light py-1 px-3" id="exampleModalLabel">Donate ERG</h5>
		  			<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
				</div>
				<div class="modal-body">
					<div class="text-break pb-3">If you liked the website <strong>you can invite me to a coffee,</strong> I will be happy to drink it to your health.</div>
					<div><span class="small"><strong>ERG address: </strong>9ggm3WB6e2BMbbCD3C9PJcgm5wbvmvCUG7w5rgszbKVWrVr8fSE</span></div>
					<div><img src={imageDonation} alt="QR wallet address"/></div>
				</div>
	  		</div>
		</div>
  	</div>

	<!-- Modal -->
	<div class="modal fade " id="modalFaq" tabindex="-1" aria-labelledby="exampleModalLabelFaq" aria-hidden="true">
		<div class="modal-dialog modal-lg">
			<div class="modal-content bg-light">
				<div class="modal-header bg-secondary">
					<h5 class="modal-title bg-dark text-light py-1 px-3" id="exampleModalLabelFaq">FAQ</h5>
					<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
				</div>
				<div class="modal-body">
					<div class="accordion" id="accordionFlushFaq">
						<p><small>ErgoNFTs.org is a website created by the community to <strong>view information on nfts</strong> created on the Ergo blockchain.</small></p>
						<p class="my-3"><small>For other questions or improvements you can <a href="https://github.com/ladopixel/ergonfts/issues" target="_blank">create an issues on github.</a></small></p>
						
						<div class="accordion-item">
							<h2 class="accordion-header" id="flush-headingOne">
								<button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseOne" aria-expanded="false" aria-controls="flush-collapseOne">
									What can I do in ergonfts.org?
								</button>
							</h2>
							<div id="flush-collapseOne" class="accordion-collapse collapse" aria-labelledby="flush-headingOne" data-bs-parent="#accordionFlushFaq">
								<div class="accordion-body">
									<p>Ergonfts.org is used to list nfts:</p>
									<ul>
										<li><strong>Add unlimited wallets </strong>as favorites so you don't have to type them anymore.</li>
										<li>Add favorite nfts from different wallets to <strong>create your favorites list.</strong></li>
										<li>All NFTs of an address >> <a href="https://ergonfts.org/#/yourAddress" target="_blank">https://ergonfts.org/#/yourAddress</a></li>
										<li>Details of a specific NFT >> <a href="https://ergonfts.org/#/?token=tokenID" target="_blank">https://ergonfts.org/#/?token=tokenID</a></li>
									</ul>
								</div>
							</div>
						</div>
						
						<div class="accordion-item">
							<h2 class="accordion-header" id="flush-headingTwo">
								<button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseTwo" aria-expanded="false" aria-controls="flush-collapseTwo">
									Can I sell or auction an NFT from this website?
								</button>
							</h2>
							<div id="flush-collapseTwo" class="accordion-collapse collapse" aria-labelledby="flush-headingTwo" data-bs-parent="#accordionFlushFaq">
								<div class="accordion-body">
									This is not possible from this website, you can auction your NFT at <a href="https://ergoauctions.org" target="_blank">https://ergoauctions.org.</a>
								</div>
							</div>
						</div>
						
						<div class="accordion-item">
							<h2 class="accordion-header" id="flush-headingThree">
								<button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseThree" aria-expanded="false" aria-controls="flush-collapseThree">
									Can I create an NFT from ErgoNFTs.org?
								</button>
							</h2>
							<div id="flush-collapseThree" class="accordion-collapse collapse" aria-labelledby="flush-headingThree" data-bs-parent="#accordionFlushFaq">
								<div class="accordion-body">
									This is not possible from this website, you can create an NFT at <a href="https://ergoutils.org" target="_blank">https://ergoutils.org.</a>	
								</div>
							</div>
						</div>
						
						<div class="accordion-item">
							<h2 class="accordion-header" id="flush-headingFour">
								<button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseFour" aria-expanded="false" aria-controls="flush-collapseFour">
									Structure for adding metadata in my NFTs.
								</button>
							</h2>
							<div id="flush-collapseFour" class="accordion-collapse collapse" aria-labelledby="flush-headingFour" data-bs-parent="#accordionFlushFaq">
								<div class="accordion-body">
									<p>This is the example for a <strong>metadata structure </strong>for your NFT, remember to enter it in the description (R5) of your NFT when you are creating it in ergoUtils.org.</p>
									<pre><code>
										{structureMetadata}
									</code></pre>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>