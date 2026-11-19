<script>
    import { skillsets } from "$lib/data.js";
    import Fa from "svelte-fa";
    import { faCode, faDatabase, faMobileScreenButton } from "@fortawesome/free-solid-svg-icons";
    import { faGitAlt, faJs, faNodeJs, faPhp, faPython, faReact } from "@fortawesome/free-brands-svg-icons";

    const devicon = (name) => `https://devicons.io/devicons/font/${name}.svg`;

    const icons = {
        TypeScript: { type: "svg", src: devicon("typescript"), alt: "TypeScript" },
        JavaScript: { type: "fa", icon: faJs },
        "Express.js": { type: "svg", src: devicon("express"), alt: "Express.js" },
        "Node.js": { type: "fa", icon: faNodeJs },
        SvelteKit: { type: "svg", src: devicon("svelte"), alt: "SvelteKit" },
        NextJS: { type: "svg", src: devicon("nextjs-icon"), alt: "Next.js" },
        ReactJS: { type: "fa", icon: faReact },
        SQL: { type: "fa", icon: faDatabase },
        MongoDB: { type: "svg", src: devicon("mongodb"), alt: "MongoDB" },
        Python: { type: "fa", icon: faPython },
        PHP: { type: "fa", icon: faPhp },
        Pug: { type: "svg", src: devicon("pug"), alt: "Pug" },
        "React Native": { type: "fa", icon: faMobileScreenButton },
        Git: { type: "fa", icon: faGitAlt },
    };

    const fallback = { type: "fa", icon: faCode };
</script>

<section id="skillsets" class="section">
    <h2 class="section-title">Tech Stacks n Skills</h2>
    <ol class="timeline">
        {#each skillsets as job}
            <li>
                <div class="when">{job.date}</div>
                <div class="what">
                    <p>{job.summary}</p>
                    <ul>
                        {#each job.tags as tag}
                            {@const item = icons[tag] ?? fallback}
                            <li class="chip">
                                {#if item.type === "svg"}
                                    <span class="devicon" style="--icon: url('{item.src}')" aria-label={item.alt} role="img"></span>
                                {:else}
                                    <Fa icon={item.icon} size="sm" />
                                {/if}
                                {tag}
                            </li>
                        {/each}
                    </ul>
                </div>
            </li>
        {/each}
    </ol>
</section>

<style>
    .chip {
        display: inline-flex;
        align-items: center;
        gap: 0.45rem;
    }

    .chip :global(svg) {
        color: var(--accent);
    }

    .devicon {
        width: 0.9em;
        height: 0.9em;
        flex: 0 0 0.9em;
        background: var(--accent);
        -webkit-mask: var(--icon) center / contain no-repeat;
        mask: var(--icon) center / contain no-repeat;
    }

    .timeline {
        list-style: none;
        border-left: 1px solid var(--line-strong);
        margin-left: 0.4rem;
    }

    .timeline > li {
        position: relative;
        display: grid;
        grid-template-columns: 11rem 1fr;
        gap: 1.5rem;
        padding: 0 0 clamp(2rem, 6vw, 2.75rem) 2rem;
    }

    .timeline > li::before {
        content: "";
        position: absolute;
        left: -6px;
        top: 0.45rem;
        width: 11px;
        height: 11px;
        border-radius: 50%;
        background: var(--bg);
        border: 2px solid var(--accent);
    }

    .timeline > li:first-child::before {
        background: var(--accent);
        box-shadow: 0 0 12px var(--accent);
    }

    .when {
        font-family: var(--mono);
        font-size: 0.85rem;
        color: var(--subtle);
        padding-top: 0.15rem;
    }

    p {
        color: var(--muted);
        margin: 0.5rem 0 0.9rem;
        max-width: 65ch;
    }

    ul {
        display: flex;
        flex-wrap: wrap;
        gap: 0.4rem;
        list-style: none;
    }

    @media (max-width: 720px) {
        .timeline > li {
            grid-template-columns: 1fr;
            gap: 0.3rem;
            padding-left: 1.5rem;
        }
    }
</style>
