<script>
    // 1 Set = "Aufgabe"
    // 1 Set hat Dauer und Pausedauer
    // 1 Runde enthält X Sets
    // Rundenhäufig
    // Pause zwischen den Runden

    let setsPerRound = 8;
    let setDuration = 45;
    let setBreakDuration = 15;
    let rounds = 3;
    let roundBreak = 60;

    let currentRound = 1;
    let currentSet = 1;
    let state = '';
    let time = 0;
    let active = false;
    let disabled = false;
    let done = false;
    let notificationsAllowed = false;

    const audioHint = new Audio('/tone.mp3');
    let canPlay = false;
    audioHint.addEventListener("canplaythrough", event => {
        canPlay = true;
    });

    function askForPermission() {
        if ("Notification" in window) {
            if (Notification.permission !== 'denied') {
                Notification.requestPermission(function (permission) {
                    // If the user accepts, let's create a notification
                    if (permission === "granted") {
                        notificationsAllowed = true;
                    }
              });
            }
        }
    };

    function notify(string) {
        if (notificationsAllowed) {
            new Notification(string);
        }
        audioHint.play();
    }

    async function run() {
        disabled = false;
        disabled = true;
        askForPermission();
        for (currentRound = 1; currentRound <= rounds; currentRound++) {
            for (currentSet = 1; currentSet <= setsPerRound; currentSet++) {
                state = 'active';
                active = true;
                notify("Interval: Let's go!")
                await startInterval(setDuration);

                // prevent duplicate breaks
                if (currentSet === setsPerRound) {
                    break;
                }

                notify("Interval: Pause!");
                state = 'break';
                active = false;
                await startInterval(setBreakDuration);
            }

            if (currentRound === rounds) {
                break;
            }
            state = 'round-break';
            active = false;
            notify("Interval: Pause!");
            await startInterval(roundBreak);
        }
        done = true;
        notify("Interval: Done 🎉!");
        disabled = false;
    }

    async function startInterval(duration) {
        return new Promise((resolve, reject) => {
            const intervalInterval = setInterval(intervalCounter, 1000);
            time = duration;
            function intervalCounter() {
                if (time == 1) {
                    clearInterval(intervalInterval);
                    return resolve();
                }
                time -= 1;
            }
        });
    }
</script>

<main class="state--{state}">
    <div class="container">
        {#if done}
            <h2>done 🎉</h2>
        {/if}
        {#if !disabled}
        <form on:submit={run}>
            <p>I choose to do <input type="number" bind:value={setsPerRound}> sets of <input type="number" bind:value={setDuration}> seconds with <input type="number" bind:value={setBreakDuration}> seconds break in between.</p>
            <p>Between each of the <input type="number" bind:value={rounds}> rounds I need a break of <input type="number" bind:value={roundBreak}> seconds.</p>
            <button on:click={run} {disabled}>start</button>
        </form>
        {:else}
            <h2>{#if active}Let's Go!{:else}Pause{/if}</h2>
            <p class="timer">{time}</p>
            <ul>
                <li>set: {currentSet} / { setsPerRound }</li>
                <li>round: {currentRound} / { rounds }</li>
            </ul>
        {/if}
    </div>
</main>

<style>
    main {
        min-height: 100vh;
        margin: 0 auto;
    }

    .state--active {
        background-color: #8ebcbb;
    }
    .state--break {
        background-color: #c8553d;
    }
    .state--round-break {
        background-color: #f28f3b;
    }
    input[type="number"] {
        width: 7ch;
        background-color: transparent;
        background-image: none;
        border: 0;
        border-bottom: 1px solid black;
        border-radius: 0;
        -webkit-box-shadow: none;
        -moz-box-shadow: none;
        box-shadow: none;
    }

    button {
        background: #000;
        border: none;
        color: #fff;
        cursor: pointer;
        font-size: inherit;
        letter-spacing: .5px;
        line-height: inherit;
        margin: 1rem 0;
        padding: .5rem 3rem;
    }

    .timer {
        font-size: 5rem;
    }

    ul {
        list-style: none
    }

    li {
        display: inline-block;
    }

    li:not(:last-of-type):after {
        content: '|';
        margin: 0 1ch;
    }

    @media (orientation: portrait) {
        h2 {
            font-size: 15vw;
        }
        .timer {
           font-size: 50vw;
        }

        li {
            font-size: 4vh;
        }
    }

</style>
