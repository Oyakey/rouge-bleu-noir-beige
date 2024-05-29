<script lang="ts">
    import SvgIcon from '@jamescoyle/svelte-icon';
    import { mdiRefresh, mdiShare, mdiClipboardText } from '@mdi/js';
    import seedrandom from 'seedrandom';
    import toast, { Toaster } from 'svelte-french-toast';
    import 'svelte-french-toast';

    let seed = new URL(document.URL).searchParams.has('seed')
        ? new URL(document.URL).searchParams.get('seed')
        : generateSeed();
    let rng = seedrandom(seed);

    $: pageUrl =
        new URL(document.URL).origin +
        new URL(document.URL).pathname +
        '?seed=' +
        seed;

    function generateSeed() {
        let seed = '';
        for (let index = 0; index < 12; index++) {
            seed += Math.floor(Math.random() * 10);
        }
        return seed;
    }

    let starter;
    let grid = generate();

    function generate() {
        const grid = [
            ...Array(8).fill('red'),
            ...Array(8).fill('blue'),
            'black',
            ...Array(7).fill('beige'),
        ];

        if (rng() > 0.5) {
            grid.push('red');
            starter = 'red';
        } else {
            grid.push('blue');
            starter = 'blue';
        }

        for (let i = grid.length - 1; i > 0; i--) {
            const j = Math.floor(rng() * (i + 1));
            [grid[i], grid[j]] = [grid[j], grid[i]];
        }

        return grid;
    }
</script>

<Toaster />

<section
    class="h-0 relative -top-24 flex items-baseline gap-4 w-0
whitespace-nowrap"
>
    <h1>rbnb</h1>
    <span class="text-base">(rouge, bleu, noir, beige)</span>
</section>

<p class="mb-8 text-xl">
    <span
        class={[
            starter === 'red' ? 'text-red-500' : 'text-blue-500',
            'font-bold',
        ].join(' ')}
    >
        L'équipe {starter === 'red' ? 'rouge' : 'bleue'}
    </span>
    commence.
</p>

<div class="grid grid-cols-5 gap-2 mb-8">
    {#each grid as box}
        <div
            class={[
                'h-full w-full aspect-square rounded',
                box === 'red' && 'bg-red-500',
                box === 'blue' && 'bg-blue-500',
                box === 'beige' && 'bg-orange-200',
                box === 'black' && 'bg-black',
            ].join(' ')}
        ></div>
    {/each}
</div>

<p class="mb-2">Partager ma carte clé :</p>
<div class="flex justify-center gap-4 mb-8">
    {#if navigator.canShare}
        <button
            class="bg-neutral-100 dark:bg-neutral-900 flex gap-2 items-center"
            on:click={() => {
                const data = {
                    title: 'CLASSIFIED: Carte clé',
                    text: 'Collègue espion, voici ma carte clé. Attention, celle-ci est top secrète !',
                    url: pageUrl,
                };
                if (!navigator.canShare(data)) {
                    return;
                }
                navigator.share(data);
            }}
        >
            <SvgIcon type="mdi" path={mdiShare} width="18" />
            Partager
        </button>
    {/if}
    <button
        class="bg-neutral-100 dark:bg-neutral-900 flex gap-2 items-center"
        on:click={async () => {
            if (!navigator.clipboard.writeText) {
                toast.error('Impossible de copier le lien');
                return;
            }
            await navigator.clipboard.writeText(pageUrl);
            toast.success('Lien copié');
        }}
    >
        <SvgIcon type="mdi" path={mdiClipboardText} width="18" />
        Copier le lien
    </button>
</div>

<button
    class="bg-transparent border-2 border-neutral-600 dark:border-white hover:underline flex gap-2 items-center"
    id="regenerate"
    type="button"
    on:click={() => {
        seed = generateSeed();
        rng = seedrandom(seed);
        grid = generate();
    }}
>
    <SvgIcon type="mdi" path={mdiRefresh} width="18" />
    Générer une nouvelle carte clé.
</button>
