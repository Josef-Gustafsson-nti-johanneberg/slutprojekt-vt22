<script>
	let sekunder = 0
	let mines = 30
	let mines_left = mines
	let columns = 15
	let amount_of_squares = columns *10
	let data = [];
	let firstKlick = true
	let GameOver = false
	let boom_sound = new Audio('../audio/boom.mp3');
	let victory_sound = new Audio('../audio/victory.mp3');

	function timer(){
		if(!GameOver){
			sekunder = sekunder +1
		}
	}
	setInterval(() => {timer()}, 1000);

	function start(){
		for(let i=amount_of_squares-1; i >= 0; i--){
			let square = {
				id: i,
				clicked: false,
				mine: false,
				flagged: false,
				mines_near_by: ""
			}
			data.push(square)
		}
		create_mines()
	}

	function create_mines(){
		for(let i=mines; i>0;i--){
			let random = Math.floor(Math.random() * amount_of_squares)
			while(data[random].mine){
				random = Math.floor(Math.random() * amount_of_squares)
			}
			data[random].mine = true
			data[random].mines_near_by = "x"
		}
		calculate_mines(data)
	}

	function calculate_mines(data_array){
		data_array = [...data_array].reverse()
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
				if(index>columns-1 && data_array[index - columns].mine){
					data_array[index].mines_near_by ++
				}
				if(index>columns && !left_edge && data_array[index - columns-1].mine){
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
		document.querySelector('#smily').innerHTML = '<img src="../img/dead.svg" alt="Status emoji">'
		for(let index=0; index < data.length; index++){
			if (data[index].mine){
				display_square_number(data[index])
			}
		}
		boom_sound.play();
		GameOver = true
	}

	function display_square_number(data){
		document.getElementById(data.id).classList.toggle("unclicked")
		data.clicked = true
		if(data.mine){
			document.getElementById(data.id).innerHTML = '<img class="bomb" src="../img/bomb.svg" alt="bomb">'
		}else{
			document.getElementById(data.id).innerHTML = data.mines_near_by		
		}	
	}
	
	function clicked_square(dataa){
		if(!GameOver){
			if(firstKlick){
				while ([...data].reverse()[dataa.id].mines_near_by !== ""){
					for(let i=amount_of_squares-1; i >= 0; i--){
						data[i].mine = false
					}
					create_mines()
				}
				firstKlick = false
			}
			if(dataa.mine && !dataa.flagged){
				game_over()
			}else if(!dataa.clicked && !dataa.flagged){
				display_square_number([...data].reverse()[dataa.id])
				if (dataa.mines_near_by == 0){
					explode_squares_near_by(data, dataa.id)
				}
			}
		}
		check_if_won()			
	}

	function check_if_won(){
		if(mines_left == 0){
			let amount_of_squares_clicked = 0
			let amount_flagged_correctly = 0
			for(let i=amount_of_squares-1; i >= 0; i--){
				if(data[i].mine && data[i].flagged){
					amount_flagged_correctly ++
				}
				if(!data[i].mine && data[i].clicked){
					amount_of_squares_clicked ++
				}
			}
			if (amount_flagged_correctly == mines && amount_of_squares_clicked == amount_of_squares - mines){
				document.querySelector('#smily').innerHTML = '<img src="../img/cool.svg" alt="status emoji">'
				GameOver = true
				victory_sound.play();
			}
		}
	}

	function right_click(dataa){
		if(!GameOver){
			if(!dataa.clicked){
				if (dataa.flagged){
					document.getElementById(dataa.id).innerHTML = ""
					dataa.flagged = false
					mines_left ++
				}else if(mines_left >0){
					dataa.flagged = true
					document.getElementById(dataa.id).innerHTML = '<img class="bomb" src="../img/flag.svg" alt="flag">'
					mines_left --
				}
			}
			check_if_won()
		}
		
	}

	function restart(){
		for(let i=amount_of_squares-1; i >= 0; i--){
			if(data[i].clicked){
				document.getElementById(data[i].id).classList.toggle("unclicked")
			}
			document.getElementById(data[i].id).innerHTML = ""
		}
		sekunder = 0
		mines_left = mines
		data = [];
		firstKlick = true
		GameOver = false
		document.querySelector('#smily').innerHTML = '<img src="../img/happy.svg" alt="status emoji">'

		start()
	}
	start()

</script>

<main>
	<header>
		<h1>Svelte Mine Sweeper</h1>
	</header>
	<section id = "info">
		<article>
			<p>{mines_left}</p>
		</article>
		<article id="smily" on:click={()=>restart()}>
			<img src="../img/happy.svg" alt="status emoji">
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

	@import './variables';
	@import url('https://fonts.googleapis.com/css2?family=MuseoModerno:wght@900&display=swap');

	@mixin border_2px{
		box-sizing: border-box;
		border: 2px solid;
	}
	@mixin border_10px{
		border: 10px solid;
		border-color:  $darkGray $lightGray $lightGray $darkGray;
	}

	@mixin center{
		display: flex;
		align-items: center;
	}

	*{
		margin: 0px;
		padding: 0px;
		font-family: 'MuseoModerno', cursive;
	}
	main{
		@include center;
		width: 100vw;
		height: 100vh;
		flex-direction: column;
		background-image: linear-gradient(to bottom right, $lightGray, $darkGray);
	
		header{
			text-align: center;
			background-image: linear-gradient( $lightGray, $darkGray);
			width: 100%;
			font-size: 2rem;
		}
		#info{
			@include center;
			justify-content: space-between;
			@include border_10px();
			height: 4rem;
			width: $widht;
			box-shadow: $pinkShadow;
			background-color: $gray;
			article{
				width: 33%;
				text-align: center;
				p{
					font-size: 3rem;
					font-weight: 700;
					color: red;
				}
			}
			:global(img){
				height: 3rem;
			}
		}
		#spel_plan{
			display: grid;
			grid-template-columns: repeat($column, 2.5rem);
			@include border_10px();
			box-shadow: $pinkShadow;
		
			article{
				cursor: pointer;
				@include border_2px();
				text-align: center;
				background-color: #bbbbbb;
				border-color: $darkGray;
				width: 2.5rem;
				height: 2.5rem;
				font-size: 1.6rem;
				font-weight: 100;
				:global(img){
					width: 2.2rem;
					height: 2.2rem;
				}
			}
			.unclicked{
				@include border_2px();
				border-color: $lightGray $darkGray $darkGray $lightGray;
				background-color: $gray;
			}
		}
	}
</style>