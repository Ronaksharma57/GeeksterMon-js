
## GeeksterMon

### Step 1: Create a New HTML File

Create a new HTML file and save it as `index.html`. Add the following code to your HTML file:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="style.css">
        <title>GeeksterMon-Ronak sharma</title>
    </head>
    <body>
        <div class="content-wrapper">
            <header>
                <h1>GeeksterMon</h1>
            </header>
            <main>
                <form id="pokemon-type-filter">
                    <select name="type" class="type-filter">
                        <option value="">Types</option>
                    </select>
                    <input type="submit" value="Filter by Type"/>
                    
                    <button onClick="window.location.reload();">Reset</button>
                </form>
                <form id="pokemon-search-form" class="" action="" method="GET">
                    <input id="pokemon-search-input" type="text" name="" placeholder="Enter Pokémon Name"/>
                </form>
                <section class="form-spacers"></section>
                <section id="pokedex">
                </section>
            </main>  
        </div>
        <script src="index.js"></script>
    </body>
</html>
```

### Step 2: Create a New CSS File

Create a new CSS file and save it as `style.css`. Add the following code to your CSS file:

```css

* {
    color: #555555;
}
.content-wrapper {
    display: block;
    margin: auto;
    max-height: 1700px;
    max-width: 1700px;
}

body {
    background-color: white;
}

h1 {
    font-family: 'Grandstander', cursive;
    text-align: center;
    font-size: 70px;
    margin-top: 45px;
    margin-bottom: 20px;
}

#pokemon-search-form {
    text-align: center;
}

#pokemon-type-filter {
    text-align: center;
}

a {
    text-decoration: none;
}

a:hover {
    color:black;
}

.form-spacers{
    height: 10px;
}

.type-filter{
    margin: 10px;;
}

#pokedex {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}

.flip-card {
    margin: 20px;
    width: 200px;
    height: 275px;
    text-align: center;
    justify-content: center;
    perspective: 1000px;
    opacity: 0.95;
}

.flip-card-inner {
    -webkit-box-shadow: 3px 3px 5px 6px #ccc;
    -moz-box-shadow: 3px 3px 5px 6px #ccc;
    box-shadow: 3px 3px 5px 6px #ccc;
    border-radius: 10px;
    position: relative;
    width: 100%;
    height: 100%;
    text-align: center;
    transition: 1s;
    transform-style: preserve-3d;
}

.flip-card:hover .flip-card-inner{
    transform: rotateY(180deg);
}


.front-pokemon-card, .back-pokemon-card {
    position: absolute;
    width: 100%;
    height: 100%;
    align-items: center;
    -webkit-backface-visibility: hidden;
    backface-visibility: hidden;
}

.front-pokemon-card {
    z-index: 2;
    transform: rotateY(0);
}

.back-pokemon-card {
    transform: rotateY(180deg);
}

.front-pokemon-image, .back-pokemon-image {
    position: relative;
    top: 50px;
}

.front-poke-id, .back-poke-id {
    position: relative;
    top: -100px;
    width: 100%;
    background-color: whitesmoke;
    color: #555555;
    justify-content: left;
}

.front-pokemon-type {
    width: fit-content;
    padding: 5px;
    background-color: white;
    border-radius: 10px;
    position: relative;
    /* left: 75px; */
    margin: auto;
    bottom: -12.5px;
    font-size: small;
}

#grass {
    background-color: #9bcc50;
}

#bug {
    background-color: #729f3f;
}

#fire {
    background-color :#fd7d24;
}

#water {
    background-color: #4592c4;
}

#ice {
    background-color: #51c4e7;
}

#electric {
    background-color: #eed535;
}

#psychic {
    background-color:  #f366b9;
}


#fairy {
    background-color: #fdb9e9;
}

#ghost {
    background-color: #7b62a3;
}

#ground {
    background-color: #f7de3f;
}

#poison {
    background-color: #b97fc9;
}

#fairy {
    background-color: #fdb9e9;
}

#normal {
    background-color: #a4acaf;
}

#dragon {
    background-color: goldenrod;
}

#fighting {
    background-color: #d56723;
}

#rock {
    background-color: #a38c21;
}
```
### Step 3: Create a New js File

Create a new CSS file and save it as `index.js`. Add the following code to your CSS file:
```
const pokeTypeURL = 'https://pokeapi.co/api/v2/type/';
const pokeQueryParams = new URLSearchParams(window.location.search);
// console.log(pokeQueryParams);
const typeParams = new URLSearchParams(window.location.search);
const typeSearch = typeParams.get('type');
// console.log(typesearch);

const pokedex = document.getElementById('pokedex');
const pokemonSearchForm  = document.querySelector('#pokemon-search-form');
const pokemonTypeFilter = document.querySelector('.type-filter');

let pokemonArray = [];
let uniqueTypes = new Set();

const fetchPokemon = () => {
    const promises = [];
    for(let i=1; i<= 151; i++){
        const pokemonURL = `https://pokeapi.co/api/v2/pokemon/${i}`;
        console.log(pokemonURL);
        promises.push(fetch(pokemonURL).then(response => response.json()))
    }

    Promise.all(promises)
    .then(allPokemon =>{
        const firstGenPokemon = allPokemon.map(pokemon=>({
            frontImage: pokemon.sprites['front_default'],
            pokemon_id:pokemon.id,
            name:pokemon.name,
            type:pokemon.types[0].type.name,
            abilities:pokemon.abilities.map(ability=>ability.ability.name).join(', '),
            description:pokemon.species.url
        }))
        pokemonArray = firstGenPokemon;
        // console.log(promises);
        console.log(firstGenPokemon);
        createPokemonCards(firstGenPokemon);
    })
    .then(generateTypes);
}

fetchPokemon()

pokemonSearchForm.addEventListener('input',(event)=>{
    const filterPokemon = pokemonArray.filter(pokemon => pokemon.name.includes(event.target.value.toLowerCase()))
    clearPokedex()
    createPokemonCards(filterPokemon)
})

function clearPokedex(){
    let section = document.querySelector('#pokedex');
    section.innerHTML = ''
}

function createPokemonCards(pokemons){
    let currentPokemon = pokemons;
    if(typeSearch){
        currentPokemon = pokemons.filter(pokemon => pokemon.type.includes(typeSearch.toLowerCase()))
    }
    currentPokemon.forEach(pokemon=>{
        createPokemonCard(pokemon)
    })
}

function createPokemonCard(pokemon) {
    // total card
    const flipCard = document.createElement("div")
    flipCard.classList.add("flip-card")
    flipCard.id = `${pokemon.name}`
    pokedex.append(flipCard)
    
    // front & back container
    const flipCardInner = document.createElement("div")
    flipCardInner.classList.add("flip-card-inner")
    flipCardInner.id = `${pokemon.type}`
    flipCard.append(flipCardInner)

    // front of card
    const frontCard = document.createElement("div")
    frontCard.classList.add('front-pokemon-card')

    const frontImage = document.createElement('img')
    frontImage.src = `${pokemon.frontImage}`
    frontImage.classList.add("front-pokemon-image")

    const frontPokeName = document.createElement('h2')
    frontPokeName.innerHTML = `<a href="/pokemon.html?pokemon_id=${pokemon.pokemon_id}">${pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1)}</a>`

    const frontPokeID = document.createElement('p')
    frontPokeID.textContent = `#${pokemon.pokemon_id}`
    frontPokeID.classList.add("front-poke-id")

    const frontDescription = document.createElement("p")
    
    
    const frontPokeType = document.createElement('p')
    frontPokeType.textContent = `${pokemon.type.toUpperCase()}`
    frontPokeType.classList.add("front-pokemon-type")

    frontCard.append(frontImage, frontPokeID, frontPokeName, frontDescription, frontPokeType)
    
    // back of card
    const backCard = document.createElement("div")
    backCard.classList.add('back-pokemon-card')

    const backImage = document.createElement('img')
    backImage.src = `${pokemon.backImage}`
    backImage.classList.add("back-pokemon-image")

    const backPokeID = document.createElement('p')
    backPokeID.textContent = `#${pokemon.pokemon_id}`
    backPokeID.classList.add("back-poke-id")

    const backPokeName = document.createElement('h2')
    backPokeName.innerHTML = `<a href="/pokemon.html?pokemon_id=${pokemon.pokemon_id}">${pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1)}</a>`
    backPokeName.classList.add("back-pokemon-name")

    const backPokeAbilities = document.createElement("p")
    backPokeAbilities.innerHTML = `<p>Abilities:<br>${pokemon.abilities}<p>`
    backPokeAbilities.classList.add("back-pokemon-abilities")

    backCard.append(backImage, backPokeID, backPokeName, backPokeAbilities)
    flipCardInner.append(frontCard, backCard);
    uniqueTypes.add(pokemon.type);
}

function generateTypes(){
    uniqueTypes.forEach(type=>{
        const typeOption = document.createElement('option');
        typeOption.textContent = type.charAt(0).toUpperCase() + type.slice(1);
        typeOption.value = type;

        pokemonTypeFilter.append(typeOption)
    })
}


```