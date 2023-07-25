<script lang="ts">
    import questions from "../assets/questions.json";
    let options = [
        "Strongly disagree",
        "Disagree",
        "Neither agree nor disagree",
        "Agree",
        "Strongly Agree",
        "Not applicable",
    ];
    let selected = questions.map((_) => -1);
    $: score = NaN;
    let evaluated = false;
    let missing = selected.length;
    function calculate() {
        evaluated = true;
        let m = selected.filter((x) => x == -1);
        missing = m.length;
        let val = selected.filter((x) => x != -1 && x != 5);
        let total = val
            .map((x) => x + 1)
            .map((x, i) => (questions[i].negativ ? (x == 6 ? 0 : 6 - x) : x))
            .reduce((x, y) => x + y, 0);
        score = total / val.length;
    }
</script>

<div>
    <table border={1} rules="rows">
        <thead>
            <tr>
                <th />
                {#each options as option}
                    <th class="option">{option}</th>
                {/each}
            </tr>
        </thead>
        <tbody>
            {#each questions as question}
                <tr class="question">
                    <td>{question.question}</td>
                    {#each options as option}
                        <td>
                            <label>
                                <input
                                    type="radio"
                                    bind:group={selected[
                                        questions.indexOf(question)
                                    ]}
                                    name={question.question
                                        .toLocaleLowerCase()
                                        .replaceAll(" ", "_")}
                                    value={options.indexOf(option)}
                                />
                            </label></td
                        >
                    {/each}
                </tr>
            {/each}
        </tbody>
    </table>
    <button on:click={calculate}>Submit</button>
    {#if evaluated}
        <h2>Score: {score}</h2>
        {#if missing > 0}
            <h2>
                You missed {missing} Question(s)
            </h2>
        {/if}
    {/if}
</div>

<style>
    thead {
        position: sticky;
        top: 0;
        background: #4b6c9e;
    }
    tr > td {
        padding-bottom: 1em;
    }
    tr {
        border: 2px solid #cccccc;
    }
    th.option {
        width: 80px;
    }
</style>
