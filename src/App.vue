<script setup>
import { computed, ref } from 'vue'

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const paperPdf = asset('gauge-invariant-representation-holonomy.pdf')

const activeResult = ref('radius')

const callouts = [
  {
    label: 'If you only read one paragraph',
    text:
      'Standard representation metrics are mostly static: they compare activation sets at fixed samples. This work asks a pathwise question instead. If an input moves around a small loop, does the representation return unchanged, or does it accumulate a net twist? That twist is representation holonomy.',
  },
  {
    label: 'Conceptual distinction',
    text: 'CKA compares representation states. Holonomy compares representation transport.',
  },
  {
    label: 'Main contribution',
    text:
      'A gauge-invariant, bias-controlled estimator for local path dependence in learned representations, with invariance guarantees, a linear null, small-radius behavior, and empirical evidence beyond pointwise similarity.',
  },
]

const estimatorSteps = [
  ['Choose a loop', 'Sample a small closed path around an input, often in a local two-dimensional PCA plane.'],
  ['Compute features', 'Evaluate the chosen layer at each point along the loop.'],
  ['Fix the gauge', 'Whiten pooled features so arbitrary basis choices do not drive the comparison.'],
  ['Share neighbors', 'For each edge, choose one midpoint neighborhood for both endpoints.'],
  ['Find a subspace', 'Estimate a shared low-rank feature subspace that captures stable local variation.'],
  ['Align by SO Procrustes', 'Solve a rotation-only alignment and exclude reflections, scaling, and shear.'],
  ['Embed back', 'Act by the learned rotation inside the subspace and identity outside it.'],
  ['Compose', 'Multiply transports around the loop to obtain the holonomy matrix.'],
]

const guarantees = [
  ['Orthogonal gauge invariance', 'A global orthogonal feature-basis change conjugates the holonomy matrix, leaving the scalar score and eigen-angles unchanged.'],
  ['Affine invariance after whitening', 'Invertible affine transformations of raw features reduce to orthogonal changes after global whitening.'],
  ['Linear null', 'Affine representation maps produce identity transports and therefore zero holonomy.'],
  ['Small-radius limit', 'Under smoothness and neighbor stability, holonomy vanishes linearly as the loop radius shrinks.'],
  ['Error decomposition', 'Finite-sample error, subspace truncation, index mismatch, and genuine curvature are separated conceptually and in the bound.'],
]

const results = {
  radius: {
    tab: 'Radius',
    title: 'Holonomy grows with loop radius.',
    body:
      'Across MNIST and CIFAR settings, larger loops accumulate more path-dependent twist. This is the behavior expected from a local curvature-like probe: tiny loops approach the numerical floor, while larger local perturbations reveal more geometry.',
    stat: 'MNIST Hidden 2 slope: 6.10e-6',
  },
  layers: {
    tab: 'Layers',
    title: 'Pathwise geometry is layer dependent.',
    body:
      'Different layers show different amplitudes and radius dependence. This makes holonomy useful as a profiling tool: it can identify where a model introduces or amplifies path dependence.',
    stat: 'MNIST Hidden 2 > Hidden 1 in the reported radius slice',
  },
  cka: {
    tab: 'Beyond CKA',
    title: 'High pointwise similarity does not imply flat transport.',
    body:
      'Representations can remain highly aligned under pointwise similarity metrics while still exhibiting nontrivial holonomy. The method isolates a pathwise effect that static overlap metrics do not measure.',
    stat: 'Aligned CKA can remain near 0.987 while loop twist remains measurable',
  },
  training: {
    tab: 'Training',
    title: 'Holonomy forms during optimization.',
    body:
      'Training dynamics show holonomy rising early and later stabilizing, suggesting that part of representation formation can be read as the emergence of a structured feature-transport field.',
    stat: 'MNIST dynamics rise early, then plateau',
  },
  robustness: {
    tab: 'Robustness',
    title: 'Holonomy participates in robustness tradeoffs.',
    body:
      'Across training regimes, holonomy shows descriptive associations with adversarial and corruption stress. It is not a universal quality score, but it gives a direct probe of pathwise geometry in the robustness story.',
    stat: 'CIFAR-10 layer2 AdvPGD: 4.74e-7 at r = 0.10',
  },
}

const activeResultCard = computed(() => results[activeResult.value])

const figures = [
  {
    src: 'figures/holonomy_schematic.png',
    title: 'Loop intuition',
    caption:
      'A loop in input space induces local transports in representation space. Composing the transports yields the holonomy matrix.',
  },
  {
    src: 'figures/mnist_h1_ci.png',
    title: 'MNIST Hidden 1',
    caption: 'Holonomy increases with loop radius in the first hidden layer.',
  },
  {
    src: 'figures/mnist_h2_ci.png',
    title: 'MNIST Hidden 2',
    caption: 'The deeper MNIST layer shows larger radius dependence in this setting.',
  },
  {
    src: 'figures/cifar10_l1_ci.png',
    title: 'CIFAR-10 layer1',
    caption: 'Layer-wise holonomy curves remain positive and stable across training regimes.',
  },
  {
    src: 'figures/cifar10_l2_ci.png',
    title: 'CIFAR-10 layer2',
    caption: 'Layer2 separates training regimes while keeping the same loop protocol.',
  },
  {
    src: 'figures/mnist_dynamics.png',
    title: 'Training dynamics',
    caption: 'Holonomy rises during early optimization and then stabilizes.',
  },
  {
    src: 'figures/eig_angles_cifar10_layer2.png',
    title: 'Eigen-angle spectrum',
    caption: 'The full holonomy matrix reveals how twist is distributed across feature directions.',
  },
  {
    src: 'figures/holonomy_mnist.png',
    title: 'Equivariance sanity check',
    caption: 'An aliased CNN accumulates much larger holonomy than a nearly equivariant translation setup.',
  },
]

const faqs = [
  ['What does nonzero holonomy mean?', 'Local representation transport around the probed loop is not path-independent. The feature field accumulates a residual rotation.'],
  ['Does higher holonomy mean a better model?', 'No. Holonomy is a geometric diagnostic, not a model-quality score. Its meaning depends on layer, loop family, and training regime.'],
  ['Is this just another similarity metric?', 'Not exactly. It complements similarity metrics by measuring transport and path dependence rather than static overlap.'],
  ['Why use loops?', 'A loop is the smallest structure that can reveal whether local transport accumulates a net twist. A single step cannot show that.'],
  ['Why is whitening important?', 'Whitening fixes a global feature gauge and prevents arbitrary scaling or basis choices from becoming the signal.'],
  ['Why restrict to rotations?', 'The target is orientation change. Reflections, scaling, and shear would confound that interpretation.'],
]
</script>

<template>
  <main>
    <nav class="topbar" aria-label="Page sections">
      <a href="#overview">Overview</a>
      <a href="#idea">Idea</a>
      <a href="#estimator">Estimator</a>
      <a href="#results">Results</a>
      <a href="#figures">Figures</a>
      <a href="#faq">FAQ</a>
      <a href="#paper">Paper</a>
    </nav>

    <article class="paper-page">
      <header class="paper-header" id="overview">
        <h1>Gauge-Invariant Representation Holonomy</h1>
        <p class="subtitle">A geometric view of how neural representations change along input paths</p>
        <p class="authors">Vasileios Sevetlidis and George Pavlidis</p>
        <p class="affiliation">Athena Research Center, Kimmeria Campus, Xanthi, Greece</p>
        <div class="actions" aria-label="Primary links">
          <a class="button primary" :href="paperPdf" target="_blank" rel="noreferrer">Read the PDF</a>
          <a class="button" href="#estimator">See the estimator</a>
        </div>
      </header>

      <section class="opening">
        <div class="lead-copy">
          <p>
            Deep networks do more than map inputs to outputs. They build internal
            representations whose geometry matters: features align, separate,
            rotate, bend, and reorganize as the input changes. That geometry can
            shape robustness, invariance, and generalization.
          </p>
          <p>
            Most popular tools for comparing learned representations are static.
            CKA, SVCCA, PWCCA, and RSA compare activation sets at fixed samples.
            They tell us whether two representations look similar pointwise, but
            not how a representation moves as the input moves.
          </p>
        </div>
        <aside class="question-box">
          <span>Core question</span>
          <strong>If we move the input around a small closed loop, what happens to the representation?</strong>
        </aside>
      </section>

      <section class="callout-grid" aria-label="Key ideas">
        <article v-for="item in callouts" :key="item.label" class="callout">
          <p>{{ item.label }}</p>
          <strong>{{ item.text }}</strong>
        </article>
      </section>

      <section id="idea" class="split-section">
        <div>
          <h2>The loop reveals what pointwise metrics miss</h2>
          <p>
            Imagine a layer representation as a field over input space. For every
            input x, the layer produces a feature vector z(x). Move x around a
            small loop. The feature vector traces a corresponding path in
            representation space.
          </p>
          <p>
            For each small step, the method estimates the best rotation aligning
            nearby feature geometry from one endpoint to the next. These local
            rotations are composed around the loop. If the representation is
            locally flat, the product is identity. If it has curvature-like path
            dependence, the product is not identity.
          </p>
          <p class="distinction">Pointwise metrics compare representation states. Holonomy compares representation transport.</p>
        </div>
        <figure class="featured-figure">
          <img :src="asset('figures/holonomy_schematic.png')" alt="Schematic of holonomy around an input loop" />
          <figcaption>
            A closed input loop induces edge-wise transports. Their product is
            the holonomy matrix, whose gap from identity measures residual twist.
          </figcaption>
        </figure>
      </section>

      <section class="analogy-band">
        <h2>Parallel transport, but for learned features</h2>
        <p>
          On a flat plane, a transported vector returns unchanged after a loop.
          On a curved surface, it can return rotated. This paper applies that
          logic to representation fields: the statistic asks whether local feature
          transport is globally path-independent around the loop being probed.
        </p>
      </section>

      <section id="estimator">
        <h2>A bias-controlled mechanism for measuring path-dependent feature rotation</h2>
        <div class="step-list">
          <article v-for="([title, text], index) in estimatorSteps" :key="title" class="step-card">
            <span>{{ index + 1 }}</span>
            <div>
              <h3>{{ title }}</h3>
              <p>{{ text }}</p>
            </div>
          </article>
        </div>
      </section>

      <section class="equation-band" aria-label="Holonomy equation">
        <p class="equation">
          H(γ) = R<sub>L-1</sub> ··· R<sub>1</sub> R<sub>0</sub>,
          &nbsp; h<sub>norm</sub> = ||H(γ) - I||<sub>F</sub> / 2√p
        </p>
        <p>
          Near zero means locally flat transport along the chosen loop. Larger
          values mean stronger accumulated rotation. The eigen-angle spectrum
          shows how that twist is distributed across feature directions.
        </p>
      </section>

      <section class="split-section">
        <div>
          <h2>Feature coordinates should not become the result</h2>
          <p>
            Internal feature axes are partly arbitrary. A layer can often be
            reparameterized by a global change of basis without changing the
            network function. The estimator fixes a global gauge by whitening and
            then uses shared subspaces with rotation-only alignment.
          </p>
          <p>
            The measurement is designed to reflect representation geometry, not
            arbitrary coordinate choices.
          </p>
        </div>
        <div class="guarantee-list">
          <article v-for="([title, text]) in guarantees" :key="title">
            <h3>{{ title }}</h3>
            <p>{{ text }}</p>
          </article>
        </div>
      </section>

      <section id="results" class="results-section">
        <div>
          <h2>What the empirical story shows</h2>
          <div class="tabs" role="tablist" aria-label="Result views">
            <button
              v-for="(item, key) in results"
              :key="key"
              type="button"
              :class="{ active: activeResult === key }"
              @click="activeResult = key"
            >
              {{ item.tab }}
            </button>
          </div>
        </div>
        <article class="result-reader">
          <h3>{{ activeResultCard.title }}</h3>
          <p>{{ activeResultCard.body }}</p>
          <strong>{{ activeResultCard.stat }}</strong>
        </article>
      </section>

      <section id="figures" class="figure-gallery">
        <h2>Selected figures from the paper</h2>
        <div class="figure-grid">
          <figure v-for="figure in figures" :key="figure.src">
            <img :src="asset(figure.src)" :alt="figure.title" loading="lazy" />
            <figcaption>
              <strong>{{ figure.title }}</strong>
              {{ figure.caption }}
            </figcaption>
          </figure>
        </div>
      </section>

      <section class="split-section">
        <div>
          <h2>What the score is, and what it is not</h2>
          <p>
            Representation holonomy is a local geometric diagnostic. It depends
            on the loop family being probed and should not be read as a global
            invariant of the data manifold or as a generic score of model quality.
          </p>
          <p>
            Its scalar form intentionally targets rotation-like path dependence.
            Effects dominated by scaling or shear are outside the main target of
            the method.
          </p>
        </div>
        <figure class="featured-figure">
          <img :src="asset('figures/pool_stability_mnist_hidden1.png')" alt="Pool size stability plot" loading="lazy" />
          <figcaption>
            Stability diagnostics help separate genuine pathwise geometry from
            estimator artifacts.
          </figcaption>
        </figure>
      </section>

      <section id="faq" class="faq-section">
        <h2>Common questions</h2>
        <div class="faq-list">
          <article v-for="([question, answer]) in faqs" :key="question">
            <h3>{{ question }}</h3>
            <p>{{ answer }}</p>
          </article>
        </div>
      </section>

      <section class="closing">
        <h2>Not just what features are, but how they move</h2>
        <p>
          Learned representations are not fully characterized by pointwise
          similarity. There is also the question of whether local representation
          transport is path-independent, and whether small loops accumulate a
          geometric twist. Representation holonomy measures that twist in a
          principled, gauge-invariant way.
        </p>
      </section>

      <section id="paper" class="paper-embed">
        <div>
          <h2>Full PDF</h2>
          <p>
            The manuscript is bundled with the site so the project page and paper
            stay together on GitHub Pages.
          </p>
          <a class="button primary" :href="paperPdf" target="_blank" rel="noreferrer">Open PDF</a>
        </div>
        <iframe :src="paperPdf" title="Gauge-invariant representation holonomy PDF"></iframe>
      </section>
    </article>
  </main>
</template>
