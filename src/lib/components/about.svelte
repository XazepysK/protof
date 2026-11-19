<script>
    import { about, profile } from "$lib/data.js";
</script>

<section id="about" class="section">
    <h2 class="section-title">About</h2>
    <div class="grid">
        <div class="bio">
            {#each about as para}
                <p>{para}</p>
            {/each}
        </div>
        <aside class="card">
            <img
                src="https://avatars.githubusercontent.com/u/258943769?v=4"
                alt="Portrait of {profile.name}"
                width="96"
                height="96"
                loading="lazy"
                decoding="async"
            />
            <dl>
                <dt>Based in</dt>
                <dd>{profile.location}</dd>
                <dt>Education</dt>
                <dd>Institute of Technology and Science Nadhlathul Ulama - Pekalongan</dd>
                <dt>Also</dt>
                <dd>Backend Developers</dd>
            </dl>
        </aside>
    </div>
</section>

<style>
    .grid {
        display: grid;
        grid-template-columns: 1.6fr 1fr;
        gap: clamp(1.75rem, 5vw, 3rem);
        align-items: center;
    }

    .bio p {
        color: var(--fg);
        font-size: clamp(1.2rem, 2.2vw, 1.5rem);
        line-height: 1.6;
        letter-spacing: -0.01em;
        max-width: 34ch;
    }

    .card {
        background: var(--bg-card);
        border: 1px solid var(--line);
        border-radius: 16px;
        padding: 1.5rem;
    }

    .card img {
        display: block;
        border-radius: 50%;
        border: 2px solid var(--accent);
        margin-bottom: 1rem;
    }

    dt {
        font-family: var(--mono);
        font-size: 0.75rem;
        color: var(--accent);
        text-transform: uppercase;
        letter-spacing: 0.06em;
        margin-top: 0.8rem;
    }

    dd {
        color: var(--fg);
        font-size: 0.95rem;
    }

    @media (max-width: 820px) {
        .grid {
            grid-template-columns: 1fr;
        }
    }
</style>
