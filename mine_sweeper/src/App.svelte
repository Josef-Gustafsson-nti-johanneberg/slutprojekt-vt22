<script>
	var sekunder = 0
	var mines = 10
	var mines_left = mines
	let amount_of_squares = 100
	let columns = 10
	let data = [];
	let firstKlick = true
	let GameOver = false

	function timer(){
		if(!GameOver){
			sekunder = sekunder +1
		}
	}
	setInterval(() => {timer()}, 1000);

	function start(){
		data = []
		for(let i=amount_of_squares-1; i >= 0; i--){
			let square = {
				id: i,
				clicked: false,
				id: i,
				mine: false,
				flagged: false,
				mines_near_by: ""
			}
			data.push(square)
		}
		create_mines()
		calculate_mines(data)
	}

	function create_mines(){
		for(let i=mines; i>0;i--){
			var random = Math.floor(Math.random() * amount_of_squares)
			while(data[random].mine){
				var random = Math.floor(Math.random() * amount_of_squares)
			}
			data[random].mine = true
			data[random].mines_near_by = "x"
		}
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
		if(id>0 && !left_edge && reverse_data[(id)-1].clicked != true && reverse_data[(id)-1].flagged != true){
			reverse_data[(id)-1].clicked = true
			display_square_number(reverse_data[(id)-1])
			if (reverse_data[(id)-1].mines_near_by == 0){
				explode_squares_near_by(data, id-1)
			}
		}
		if(id>columns-1 && !right_edge && reverse_data[id+ 1- columns].clicked != true && reverse_data[id+ 1- columns].flagged != true){
			reverse_data[id+ 1- columns].clicked = true
			display_square_number(reverse_data[id+ 1- columns])
			if (reverse_data[id+ 1- columns].mines_near_by == 0){
				explode_squares_near_by(data, (id+ 1- columns))
			}
		}
		if(id>columns && reverse_data[id - columns].clicked != true && reverse_data[id - columns].flagged != true){
			reverse_data[id - columns].clicked = true
			display_square_number(reverse_data[id - columns])
			if(reverse_data[id - columns].mines_near_by == 0){
				explode_squares_near_by(data, (id - columns))
			}	
		}
		if(id>columns+1 && !left_edge && reverse_data[id - columns-1].clicked != true && reverse_data[id - columns-1].flagged != true){
			reverse_data[id - columns-1].clicked = true
			display_square_number(reverse_data[id - columns-1])
			if(reverse_data[id - columns-1].mines_near_by == 0){
				explode_squares_near_by(data, (id - columns-1))
			}
		}
		if(id<amount_of_squares-1 && !right_edge && reverse_data[id+1].clicked != true && reverse_data[id+1].flagged != true){
			reverse_data[id+1].clicked = true
			display_square_number(reverse_data[id+1])
			if(reverse_data[id+1].mines_near_by == 0){
				explode_squares_near_by(data, (id+1))
			}
		}
		if(id<(amount_of_squares-columns+1) && !left_edge && reverse_data[id + columns - 1].clicked != true && reverse_data[id + columns - 1].flagged != true){
			reverse_data[id + columns - 1].clicked = true
			display_square_number(reverse_data[id + columns - 1])
			if(reverse_data[id + columns - 1].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns - 1))
			}
		}
		if(id<(amount_of_squares-columns-1) && !right_edge && reverse_data[id + columns + 1].clicked != true && reverse_data[id + columns + 1].flagged != true){
			reverse_data[id + columns + 1].clicked = true
			display_square_number(reverse_data[id + columns + 1])
			if(reverse_data[id + columns + 1].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns + 1))
			}
		}
		if(id<(amount_of_squares-columns) && reverse_data[id + columns].clicked != true && reverse_data[id + columns].flagged != true){
			reverse_data[id + columns].clicked = true
			display_square_number(reverse_data[id + columns])
			if(reverse_data[id + columns].mines_near_by == 0){
				explode_squares_near_by(data, (id + columns))
			}
		}
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
		if(firstKlick){
			while ([...data].reverse()[dataa.id].mines_near_by !== ""){
				for(let i=amount_of_squares-1; i >= 0; i--){
					data[i].mine = false
				}
				create_mines()
				calculate_mines(data)
			}
			firstKlick = false
		}
		if(!GameOver){
			if(dataa.mine && !dataa.flagged){
				game_over()
			}else if(!dataa.clicked && !dataa.flagged){
				dataa.clicked = true
				display_square_number([...data].reverse()[dataa.id])
				if (dataa.mines_near_by == 0){
					explode_squares_near_by(data, dataa.id)
				}
			}
		}			
	}

	function right_click(dataa){
		if(!GameOver){
			if(!dataa.clicked){
				if (dataa.flagged){
					document.getElementById(dataa.id).innerHTML = " "
					dataa.flagged = false
					mines_left ++
				}else if(mines_left >0){
					dataa.flagged = true
					document.getElementById(dataa.id).innerHTML = "🚩"
					mines_left --
				}
			}
		}
		// måste vara bättre vinnst logik
		if(mines_left == 0){
			let amount_flagged_correctly = 0
			for(let i=amount_of_squares-1; i >= 0; i--){
				if(data[i].mine && data[i].flagged){
					amount_flagged_correctly ++
				}
			}
			if (amount_flagged_correctly == mines){
				console.log("du vann")
				GameOver = true

			}
		}
	}
	start()
</script>

<main>
	<header>
		<h1>Svelte Vanilla Mine Sweeper</h1>
	</header>
	<section id = "info">
		<article>
			<p>{mines_left}</p>
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
			<article id="{data.id}" class="unclicked" on:click={()=>clicked_square(data)} on:contextmenu|preventDefault={()=>right_click(data)}>
			</article>
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
			padding-top: .5rem;
			padding-bottom: .5rem;
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
			grid-template-columns: repeat(10, 2rem);
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
				text-align: center;
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