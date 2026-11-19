<script>
    import Navbar from "$lib/components/navbar.svelte";
    import Hero from "$lib/components/hero.svelte";
    import About from "$lib/components/about.svelte";
    import Experience from "$lib/components/skillsets.svelte";
    import Pipeline from "$lib/components/pipeline.svelte";
    import Github from "$lib/components/github.svelte";
    import Footer from "$lib/components/footer.svelte";
    import { profile } from "$lib/data.js";
    import { page } from "$app/state";
    import "../styles.css";

    const title = `${profile.name} | ${profile.role}`;
    const description =
        "Hello, Iam Muhammad Rijalurrobbani, Emma Backend Developers working for builds and maintains the behind-the-scenes server side of websites and apps";
    const image = "https://github.com/XazepysK.png";
</script>

<svelte:head>
    <title>{title}</title>
    <meta name="description" content={description} />
    <meta name="theme-color" content="#000000" />
    <link rel="canonical" href={page.url.origin} />
    <meta property="og:type" content="website" />
    <meta property="og:url" content={page.url.origin} />
    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    <meta property="og:image" content={image} />
    <meta name="twitter:card" content="summary" />
    <meta name="twitter:title" content={title} />
    <meta name="twitter:description" content={description} />
    <meta name="twitter:image" content={image} />

    {@html `<script type="application/ld+json">${JSON.stringify({
        "@context": "https://schema.org",
        "@type": "Person",
        name: profile.name,
        jobTitle: profile.role,
        url: page.url.origin,
        sameAs: [profile.github, profile.linkedin, profile.npm, profile.telegram],
        alumniOf: "SMK Mutiara Bangsa",
    })}</script>`}
</svelte:head>

<Navbar />
<main>
    <Hero />
    <About />
    <Experience />
    <Pipeline />
    <Github />
</main>
<Footer />
