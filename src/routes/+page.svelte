<script>
    import Nav from '$lib/components/nav.svelte';
    import Footer from '$lib/components/footer.svelte';
    import { onMount } from 'svelte';
    import { asset } from '$app/paths';

    let temp = $state("?");
    let WMOcode = $state(500);
    let city = $state("Loading...");
    let planet = $state("Loading");
    let description = $state("Loading");


    /**
	 * @param {string} planet
	 */
    function getDesc(planet) {
        const descriptions = {
            "Endor": "Temperate foggy. Watch for Ewok's",
            "Kamino": "Wet.",
            "Hoth": "Cold, Icy, Freezing Desolation.",
            "Naboo": "Temperate, dry, and fairly pleasant",
            "Coruscant": "Jedi meeting present. But outside is beautifully calm.",
            "Scariff": "Cloudy, clear, and beautiful outside.",
            "Tatooine": "Hot, Dry, Occasional Sarlacc.",
            "Bespin": "Visit Mos Eisley for a drink, its HOT.",
            "Kashyyk": "Watch for Wookies."
        };
        return descriptions[planet]
    }

    /**
	 * @param {number} temp
	 * @param {string} condition
	 */
    function getPlanet(temp, condition) {
        let planet;
        if (condition.includes("Drizzle") || condition.includes("Rain") || condition.includes("Showers")) {
            planet = 'Kamino';
        }
        else if (condition.includes("Fog")){
            planet = 'Endor';
        }
        else {
            if (temp <= 35) {
                planet = 'Hoth'
            } else if (temp <= 55) {
                planet = 'Naboo'
            } else if (temp <= 65) {
                planet = 'Coruscant'
            } else if (temp <= 72) {
                planet = 'Scariff'
            } else if (temp <= 78) {
                planet = 'Tatooine'
            } else if (temp <= 90) {
                planet = 'Bespin'
            } else {
                planet = 'Kashyyk'
            }
        }
        return planet

    }

    async function getLocation() {
        const locationData = await (await fetch("https://ipv4-check-perf.radar.cloudflare.com/api/info")).json();
        return {lat: locationData.latitude, lon: locationData.longitude, city: locationData.city}
    }

    /**
	 * @param {number} lat
	 * @param {number} lon
	 */
    async function getWeather(lat, lon) {
        const weatherData = await (await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,weather_code&wind_speed_unit=mph&temperature_unit=fahrenheit&precipitation_unit=inch`)).json();
        return weatherData
    }
    
    /**
	 * @param {number} code
	 */
    async function getCondition(code) {
        const codeData = await (await fetch(asset("/data/codes.json"))).json();
        return codeData[code].day.description
    } 

    onMount(async () => {
        const location = await getLocation();
        const weather = await getWeather(location.lat, location.lon);
        temp = weather.current.temperature_2m;
        WMOcode = weather.current.weather_code;
        city = location.city;
        planet = getPlanet(Number(temp), await getCondition(Number(WMOcode)));
        description = getDesc(planet);
    });

</script>
<main class="relative h-screen bg-slate-950 bg-cover bg-center bg-no-repeat" style={`background-image: url(${asset('/images/' + planet + '.jpg')});`}>
    <div class="absolute inset-0 bg-black/10"></div>
    <div class="relative z-10">
        <Nav location="{city}" temp="{temp}" code={WMOcode} />
        <div class="flex flex-col items-center justify-start my-40">
            <h2 class=" text-white font-sans font-normal uppercase text-xl sm:text-2xl md:text-3xl ">Feels Like</h2>
            <h1 id="planet" class="text-white font-sans font-normal tracking-[0.3em] uppercase text-5xl text-shadow-lg text-shadow-black sm:text-6xl md:text-7xl lg:text-8xl xl:text-8xl">{planet}</h1>
        </div>
    </div>
    <div class="text-2xl text-white font-normal absolute bottom-20 right-20">
        <p id="planet-description">{description}</p>
    </div>
</main>
<footer>
    <Footer />
</footer>
