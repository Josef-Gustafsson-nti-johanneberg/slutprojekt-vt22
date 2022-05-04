<script>
	var sekunder = 0
	var minuter = 0
	var mines = 40
	let amount_of_squares = 252
	let columns = 18
	let data = [];
	let GameOver = false

	function timer(){
		sekunder = sekunder +1
		// if (sekunder == 60) {
		// minuter = minuter + 1;
		// sekunder = 0;
		// }
	}
	setInterval(() => {timer()}, 1000);
	function start(){
		let GameOver = false

		for(let i=amount_of_squares-1; i >= 0; i--){
			let square = {
				id: i,
				clicked: false,
				id: i,
				mine: false,
				mines_near_by: ""
			}
			data.push(square)
		}

		for(let i=mines; i>0;i--){
			data[Math.floor(Math.random() * amount_of_squares)].mine = true
		}
		calculate_mines(data)
	}

	function calculate_mines(data_array){
		for(let index=0; index < data_array.length; index++){
			let left_edge = (index%columns == 0)
			let right_edge = (index%columns == columns-1)
			if(!data_array[index].mine){
				data_array[index].mines_near_by = 0

				if(index>0 && !left_edge && data_array[index-1].mine){
					data_array[index].mines_near_by ++
				}
				if(index>columns-1 && !right_edge && data_array[index+ 1- columns].mine){
					data_array[index].mines_near_by ++
				}
				if(index>columns && data_array[index - columns].mine){
					data_array[index].mines_near_by ++
				}
				if(index>columns+1 && !left_edge && data_array[index - columns-1].mine){
					data_array[index].mines_near_by ++
				}
				if(index<amount_of_squares-1 && !right_edge && data_array[index+1].mine){
					data_array[index].mines_near_by ++
				}
				if(index<(amount_of_squares-columns+1) && !left_edge && data_array[index + columns - 1].mine){
					data_array[index].mines_near_by ++
				}
				if(index<(amount_of_squares-columns-1) && !right_edge && data_array[index + columns + 1].mine){
					data_array[index].mines_near_by ++
				}
				if(index<(amount_of_squares-columns) && data_array[index + columns].mine){
					data_array[index].mines_near_by ++
				}
				if(data_array[index].mines_near_by == 0){
					data_array[index].mines_near_by = ""
				}

			}

		}
	}

	function explode_squares_near_by(data, id){
		let reverse_data = [...data].reverse()
		let left_edge = (id%columns == 0)
		let right_edge = (id%columns == columns-1)
		if(id>0 && !left_edge && reverse_data[(id)-1].clicked != true){
			reverse_data[(id)-1].clicked = true
			display_square_number(reverse_data[(id)-1])
			if (reverse_data[(id)-1].mines_near_by == 0){
				explode_squares_near_by(data, id-1)
			}
		}
		if(id>columns-1 && !right_edge && reverse_data[id+ 1- columns].clicked != true){
			reverse_data[id+ 1- columns].clicked = true
			display_square_number(reverse_data[id+ 1- columns])
			if (reverse_data[id+ 1- columns].mines_near_by == 0){
				explode_squares_near_by(data, (id+ 1- columns))
			}
		}
		if(id>columns && reverse_data[id - columns].clicked != true){
			reverse_data[id - columns].clicked = true
			display_square_number(reverse_data[id - columns])
			if(reverse_data[id - columns].mines_near_by == 0){
				explode_squares_near_by(data, (id - columns))
			}	
		}
		if(id>columns+1 && !left_edge && reverse_data[id - columns-1].clicked != true){
			reverse_data[id - columns-1].clicked = true
			display_square_number(reverse_data[id - columns-1])
			if(reverse_data[id - columns-1].mines_near_by == 0){
				explode_squares_near_by(data, (id - columns-1))
			}
		}
		if(id<amount_of_squares-1 && !right_edge && reverse_data[id+1].clicked != true){
			reverse_data[id+1].clicked = true
			display_square_number(reverse_data[id+1])
			if(reverse_data[id+1].mines_near_by == 0){
				explode_squares_near_by(data, (id+1))
			}
		}
		if(id<(amount_of_squares-columns+1) && !left_edge && reverse_data[id + columns - 1].clicked != true){
			reverse_data[id + columns - 1].clicked = true
			display_square_number(reverse_data[id + columns - 1])
			if(reverse_data[id + columns - 1].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns - 1))
			}
		}
		if(id<(amount_of_squares-columns-1) && !right_edge && reverse_data[id + columns + 1].clicked != true){
			reverse_data[id + columns + 1].clicked = true
			display_square_number(reverse_data[id + columns + 1])
			if(reverse_data[id + columns + 1].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns + 1))
			}
		}
		if(id<(amount_of_squares-columns) && reverse_data[id + columns].clicked != true){
			reverse_data[id + columns].clicked = true
			display_square_number(reverse_data[id + columns])
			if(reverse_data[id + columns].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns))
			}
		}
	}
	function test(){
		start()
	}

	function game_over(){
		// let button = "<button on:click={test()}>knapp</button>"
		document.querySelector('#smily').innerHTML = '<img id="smily" src="../img/sadComputer.png" alt="">'
		// // document.querySelector('#info').innerHTML = '<a href="#">länk</a>'
		// // document.querySelector('#info').innerHTML = "<button on:click={()=>test()}>knapp</button>"
		for(let index=0; index < data.length; index++){
			if (data[index].mine){
				display_square_number(data[index])
			}
		}
		GameOver = true
	}
	function display_square_number(data){
		document.getElementById(data.id).classList.toggle("unclicked")
		if(data.mine){
			document.getElementById(data.id).innerHTML = '<img class="bomb" src="../img/bomb.svg" alt="ojdå">'
		}else{
			document.getElementById(data.id).innerHTML = data.mines_near_by		
		}	
	}
	
	function clicked_square(dataa){
		if (GameOver == false){
			if (dataa.mine){
				dataa.clicked = true
				game_over()
				
			}else{
				dataa.clicked = true
				display_square_number(dataa)
				if (dataa.mines_near_by == 0){
					explode_squares_near_by(data, dataa.id)

				}

			}
		}			
	}
	start()
</script>

<main>
	<header>
		<h1>JS Vanilla Mine Sweeper</h1>
	</header>
	<section id = "info">
		<article>
			<p>{mines}</p>
		</article>
		<article id="smily" >
			<img src="../img/happyComputer.png" alt="">
		</article>
		<article>
			<p>{sekunder}</p>
		</article>
	</section>
	<section id = 'spel_plan'>
		{#each [...data].reverse() as data}
			{#if data.id %2 == 0}
			<article id="{data.id}" class="unclicked odd" on:click|once={()=>clicked_square(data)}>
				{#if !data.mine}
					<!-- <p>{data.mines_near_by}</p> -->
				{/if}
			</article>
			{:else if data.id %2 == 1}
			<article id="{data.id}" class="unclicked even" on:click|once={()=>clicked_square(data)}>
				{#if !data.mine}
					<!-- <p>{data.mines_near_by}</p> -->
				{/if}
			</article>
			{/if}
	
		{/each}
	</section>

</main>

<style lang="scss">
	*{
		margin: 0px;
		padding: 0px;
	}
	main{
		width: 100vw;
		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		background-color: #c5c5c5;
	
		header{
			text-align: center;
			background-color: #7a7a7a;
			width: 100%;
		}
		#info{
			height: 3rem;
			width: 30.6rem;
			display: flex;
			justify-content: space-evenly;
			p{
				font-size: 2rem;
				font-weight: 700;
				color: red;
			}
			:global(img){
				width: 2.9rem;
			}

		}
		#spel_plan{
			display: grid;
			grid-template-columns: repeat(18, 2rem);
			// width: 27rem;
			// gap: 0.2rem;
			// .even{
			// 	background-color: red;
			// }
			// .odd{
			// 	background-color: blue;
			// }

			article :hover{
				cursor: pointer;
			}

			article{

				// border: 1px solid black;
				background-color: #bbbbbb;
				border: 2px solid #707070;

				width: 1.7rem;
				height: 1.5rem;
				:global(img){
					width: 1.7rem;
					height: 1.5rem;
				}
			}
		


			.unclicked{
				opacity: 100%;
				border: 2px solid;
				border-color: #eeeeee #999999 #999999 #eeeeee;
				background-color: #cccccc;
				padding: 0px;
				p{
					opacity: 0%;
				}
			}
		}
	}







</style>