## Movie Search 

This is a simple movie search web that allows users to search for movies by name. The app is built using HTML, CSS, and JavaScript. 
Hosted link:https://Dakshayini1.github.io/moviesearch/

### Getting Started

To get started, clone the repository and install the dependencies.

```
git clone https://github.com/Dakshayini1/movieSearch.git
cd movieSearch
npm install
```

### Running the App

To run the app, start the development server.

```
npm start
```

## Step 1

Create a new file called `index.html` in your project folder with this content:
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Movie Search</title>
  <link rel="stylesheet" href="./style.css">
</head>
<body>
  <nav class="navbar">
    <h1>Movie Search</h1>
    <input type="text" id="searchInput" placeholder="Search movies...">
  </nav>
  <div class="container">
    <div id="movies" class="movies-list"></div>
    <div id="pagination" class="pagination"></div>
  </div>
  <script src="./script.js"></script>
</body>
</html>


```

## Step 2

### In the same directory create another file named `style.css` which will contain all of our CSS code for styling purposes. Add these styles to it

```
body {
    margin: 0;
    font-family: Arial, sans-serif;
  }
  
  .navbar {
    background-color: #333;
    color: white;
    padding: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .container {
    margin: 20px;
  }
  
  .movies-list {
    display: flex;
    flex-wrap: wrap;
    gap: 250px;
  }
  
  .pagination {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  


```

## Step 3

### Create a new file in the components folder called index.js and add this code to it:

```
const searchInput = document.getElementById('searchInput');
const moviesContainer = document.getElementById('movies');
const paginationContainer = document.getElementById('pagination');

let currentPage = 1;
let totalResults = 0;

const API_KEY = 'e94faa5f'; 
const BASE_URL = 'https://www.omdbapi.com/';
// https://www.omdbapi.com/?&apikey=e94faa5f&s=Batman&page=2

const debounce = (func, delay) => {
  let timeoutId;
  return (...args) => {
    if (timeoutId) {
      clearTimeout(timeoutId);
    }
    timeoutId = setTimeout(() => {
      func(...args);
    }, delay);
  };
};

const fetchMovies = async (searchTerm, page) => {
  const response = await fetch(`${BASE_URL}?&apikey=${API_KEY}&s=${searchTerm}&page=${page}`);
// const response = await fetch('https://www.omdbapi.com/?&apikey=e94faa5f&s=Batman&page=2')
  console.log(response);
  const data = await response.json();
  console.log(data);
  return data;
};

const displayMovies = (movies) => {
  moviesContainer.innerHTML = '';
  console.log(movies.Search);
  movies.Search.forEach(movie => {
    const movieCard = document.createElement('div');
    movieCard.classList.add('movie-card');
    movieCard.innerHTML = `
      <img src="${movie.Poster?movie.Poster:"https://www.shutterstock.com/shutterstock/photos/586719869/display_1500/stock-vector-online-cinema-art-movie-watching-with-popcorn-and-film-strip-cinematograph-concept-vintage-retro-586719869.jpg"}" alt="${movie.Title}">
      <h3>${movie.Title}</h3>
      <p>Year: ${movie.Year}</p>
    `;
    moviesContainer.appendChild(movieCard);
  });
};

const displayPagination = () => {
  const totalPages = Math.ceil(totalResults / 10);
  paginationContainer.innerHTML = `
    <button ${currentPage === 1 ? 'disabled' : ''} onclick="goToPage(${currentPage - 1})">Previous</button>
    <span>Page ${currentPage} of ${totalPages}</span>
    <button ${currentPage === totalPages ? 'disabled' : ''} onclick="goToPage(${currentPage + 1})">Next</button>
  `;
};

const goToPage = (page) => {
  if (page >= 1 && page <= Math.ceil(totalResults / 10)) {
    currentPage = page;
    searchMovies();
  }
};

const searchMovies = async () => {
    const searchTerm = searchInput.value;
    if (searchTerm.trim() === '') {
      moviesContainer.innerHTML = '<p>Please enter a search term</p>';
      paginationContainer.innerHTML = '';
      return;
    }
  
    const data = await fetchMovies(searchTerm, currentPage);
    
    if (data.Response === 'False') {
      if (data.Error === 'Too many results.') {
        moviesContainer.innerHTML = '<p>Too many results. Please provide a more specific search term.</p>';
        paginationContainer.innerHTML = '';
      } else {
        moviesContainer.innerHTML = `<p>Error: ${data.Error}</p>`;
        paginationContainer.innerHTML = '';
      }
      return;
    }
    
    totalResults = parseInt(data.totalResults);
    
    if (totalResults === 0) {
      moviesContainer.innerHTML = '<p>No results found</p>';
      paginationContainer.innerHTML = '';
      return;
    }
    
    displayMovies(data);
    displayPagination();
  };
  

const debouncedSearchMovies = debounce(searchMovies, 300);

searchInput.addEventListener('input', debouncedSearchMovies);

searchMovies();
```


