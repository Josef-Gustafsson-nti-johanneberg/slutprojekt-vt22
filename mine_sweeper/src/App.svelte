<script>
	var sekunder = 0
	var minuter = 0
	var mines = 40
	let amount_of_squares = 252
	let rows = 14
	let columns = 18
	let data = [];
	let container;

	import {onMount} from 'svelte';
	function timer(){
		sekunder = sekunder +1
		if (sekunder == 60) {
		minuter = minuter + 1;
		sekunder = 0;
		}
	}
	setInterval(() => {timer()}, 1000);

	for(let i=amount_of_squares; i > 0; i--){
		let square = {
        id: i,
        // img: `img/char-${match_id}.png`,
        clicked: false,
		id: i,
		mine: false,
		mines_near_by:0
		}
		data.push(square)
	}

	for(let i=mines; i>0;i--){
		data[i].mine = true
	}
	function randomize_array(array){
		for(let i=array.length-1; i > 0; i--){
			let j = Math.floor(Math.random() * i)
			let temp = array[i]
			array[i] = array[j]
			array[j] = temp
		}
	}
	randomize_array(data)

	function calculate_mines(array){
		
		// for(let i=0; i <=3; i++){
		// 	if (i%columns != 0 && i%columns != 1 && array[i+1].mine == true){
		// 		array[i].mines_near_by = array[i].mines_near_by +1
		// 	}
		// }
		for(let i=array.length-2; i > 0; i--){
			if (i%columns != 0 && i%columns != 1 && array[i+1].mine == true){
				array[i].mines_near_by = array[i].mines_near_by +1
			}
		}
		for(let i=1; i < array.length; i++){
			if (i%columns != 0 && i%columns != 1 && array[i-1].mine == true){
				console.log("fungerar ändp")
				array[i].mines_near_by = array[i].mines_near_by +1
			}
		}
		for(let i=0; i < array.length-18; i++){
			if (array[i+columns].mine == true){
				console.log("fungerar ändp")
				array[i].mines_near_by = array[i].mines_near_by +1
			}
		}
		for(let i=18; i < array.length-1; i++){
			if (array[i-columns].mine == true){
				console.log("fungerar ändp")
				array[i].mines_near_by = array[i].mines_near_by +1
			}
		}
	}
	calculate_mines(data)
	console.log(data)

	function render(){
	
		



	}

	render()

</script>

<main>
	<header>
        <h1>JS Vanilla Mine Sweeper</h1>
    </header>

    <h2 id="time">
        Tid sedan start Minuter: {minuter} Sekunder: {sekunder}
    </h2>
    <section id = 'spel_plan'>

		{#each data as data}
			{#if data.mine} 
			<article id="mine">
				{data.mines_near_by}
			</article>
			{:else}
			<article>
				{data.mines_near_by}
			</article>
			{/if}
	
		{/each}
    </section>
</main>

<style>
	main{
		display: flex;
		flex-direction: column;
	}
	section{
		display: grid;
        grid-template-columns: repeat(18, 2rem);
		gap: 0.2rem;
	}
	article{
            /* background-color: #160F29; */
			border: 1px solid black;
            width: 1.5rem;
            height: 1.5rem;
	}
	#mine{
		background-color: green;
	}







</style>