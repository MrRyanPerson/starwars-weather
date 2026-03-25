<script>
    import { asset } from "$app/paths";

    let { location = 'Loading', temp = "?", code = 1 } = $props();

    let description = $state();
    let src = $state();

    $effect(() => {
        const currentCode = code;
        const currentTemp = temp;
        (async () => {
            const codeData = await (await fetch(asset('/data/codes.json'))).json();
            description = codeData[currentCode].day.description + ", Temp: " + currentTemp + " °F";
            src = codeData[currentCode].day.image;
        })();
    });
</script>

<div class="bg-slate-950/30 backdrop-blur-xs p-2 flex justify-between">
	<div class="flex items-center mx-2 text-white font-light font-sans">
		<p>{location}</p>
	</div>
	<div class="mx-2 order-2 text-white font-light font-sans">
        <p class="flex items-center gap-2">
            <img class="h-6 md:h-10 object-scale-down inline" {src} alt="weathericon" />
            {description}
        </p>
	</div>
</div>