# Conceptual Quantum Key Distribution (QKD) Demo

This repository contains a simple, interactive web-based demonstration of a core principle behind Quantum Key Distribution (QKD), specifically the "measurement problem" as applied in protocols like BB84. The demo illustrates how the act of observing a quantum state inherently disturbs it, a property that can be leveraged to detect eavesdropping during cryptographic key exchange.

**Disclaimer:** This is a highly simplified conceptual model for educational purposes only. It is **NOT** a secure cryptographic implementation and should **NEVER** be used for real-world security applications.

## Table of Contents

* [Core Concepts Represented](#core-concepts-represented)

* [How the Demo Works (Step-by-Step Logic)](#how-the-demo-works-step-by-step-logic)

  * [1. Alice Generates Bits & Bases](#1-alice-generates-bits--bases)

  * [2. Eve Intercepts (Optional)](#2-eve-intercepts-optional)

  * [3. Bob Measures Photons](#3-bob-measures-photons)

  * [4. Key Reconciliation & Eavesdropping Detection](#4-key-reconciliation--eavesdropping-detection)

* [How to Use the Demo](#how-to-use-the-demo)

* [Technical Details](#technical-details)

## Core Concepts Represented

* **Bits (0s and 1s):** The secret information Alice aims to share securely with Bob.

* **Bases (Rectilinear '+' or Diagonal 'x'):** These represent conceptual polarization filters used to encode and measure the "photons."

  * **Rectilinear (0):** Measures horizontal (H) or vertical (V) polarization.

  * **Diagonal (1):** Measures diagonal (D1 or D2) polarization.

* **Measurement Disturbance:** A fundamental quantum mechanics principle where observing a quantum state (like a photon's polarization) changes it, especially if measured in a mismatched basis.

## How the Demo Works (Step-by-Step Logic)

The JavaScript code simulates the interactions between Alice, Bob, and an optional eavesdropper (Eve) to demonstrate QKD.

### Global Variables and DOM Elements

The script begins by initializing global JavaScript arrays (e.g., `aliceBits`, `aliceBases`, `bobMeasuredBits`) to maintain the state of the simulation. It also retrieves references to various HTML elements (buttons, input fields, and display areas) to interact with the user interface.

### Helper Functions

* `updateOutput(element, data)`: A utility function to update the text content of the display boxes in the HTML.

* `showMessage(message, type)`: A helper function to display status or error messages to the user within the `resultMessageDiv`, applying different styling based on the message type (success, warning, error).

### 1. Alice Generates Bits & Bases (`generateAliceKeysBtn` event listener)

* **Action:** When the "1. Alice Generates Bits & Bases" button is clicked.

* **Process:**

  * The `keyLength` is read from the input field (validated to be between 4 and 64 bits).

  * All simulation state arrays are reset.

  * Alice generates a sequence of random `keyLength` bits (0 or 1) and stores them in `aliceBits`.

  * For each bit, she also randomly selects a "polarization basis" (0 for Rectilinear, 1 for Diagonal) and stores it in `aliceBases`.

  * **Display:** The `alicePhotonsDiv` shows a conceptual representation of Alice's "sent photons" (e.g., 'H(0)', 'V(1)', 'D1(0)', 'D2(1)' based on bit and basis), and `aliceBasesDiv` shows her chosen bases ('Rect' or 'Diag').

* **Outcome:** A success message confirms Alice has generated her data and conceptually sent the photons.

### 2. Eve Intercepts (if enabled) (`eveInterceptBtn` event listener)

* **Action:** When the "2. Eve Intercepts (if enabled)" button is clicked.

* **Process:**

  * The `eavesdropChance` (percentage) is read from the input field.

  * The code iterates through each "photon" Alice sent.

  * **Eavesdropping Logic:** Based on the `eavesdropChance`, Eve decides whether to intercept a photon:

    * **If Eve intercepts:**

      * Eve randomly chooses a `basis` to measure the photon.

      * If Eve's chosen `basis` matches Alice's original `basis`, Eve measures the correct bit.

      * **Crucially:** If Eve's chosen `basis` *does not* match Alice's original `basis`, Eve's measurement yields a random bit (50% chance of being incorrect). More importantly, this incorrect measurement **modifies the `aliceBits[i]` value**, simulating the quantum disturbance.

      * Eve's measured bit and chosen basis are recorded in `eveMeasuredBits` and `eveBases`.

    * **If Eve does NOT intercept:** The photon passes through undisturbed, and 'N/A' is recorded for Eve's measurements.

* **Outcome:** The `eveMeasuredDiv` and `eveBasesDiv` are updated. A message indicates how many photons Eve intercepted and if any disturbance occurred.

### 3. Bob Measures Photons (`bobMeasureBtn` event listener)

* **Action:** When the "3. Bob Measures Photons" button is clicked.

* **Process:**

  * Bob iterates through the photons (which might have been altered by Eve).

  * For each photon, Bob randomly chooses a `basis` to measure it (`bobBases`).

  * If Bob's chosen `basis` matches Alice's *original sending basis* (`aliceBases[i]`), he measures the correct bit (or the bit that Eve might have altered).

  * If Bob's chosen `basis` *does not* match Alice's original sending basis, his measurement yields a random bit (50% chance of being incorrect).

  * Bob's measured bits and chosen bases are recorded.

* **Outcome:** The `bobMeasuredDiv` and `bobBasesDiv` are updated, and a success message is displayed.

### 4. Key Reconciliation & Eavesdropping Detection (`reconcileKeysBtn` event listener)

* **Action:** When the "4. Key Reconciliation & Eavesdropping" button is clicked.

* **Process:**

  * Alice and Bob (conceptually) publicly compare their chosen bases (`aliceBases` and `bobBases`).

  * They **only keep the bits** where their bases matched. These form `rawAliceKey` and `rawBobKey`.

  * They then compare these `rawAliceKey` and `rawBobKey` bit by bit to count `errorsDetected`.

  * **Eavesdropping Detection:**

    * If `errorsDetected` is 0, all bits where their bases matched are identical. This implies no significant disturbance, and they successfully establish a `finalSharedKey`.

    * If `errorsDetected` is greater than 0, it means discrepancies exist. This is the tell-tale sign that Eve (or significant channel noise) intercepted and disturbed the photons. In a real QKD protocol, they would immediately discard this key and attempt to establish a new one.

* **Outcome:** The `sharedKeyDiv` is updated with the final key (or empty if discarded), and a message indicates whether the key was established or discarded due to detected eavesdropping.

## How to Use the Demo

1. **Open the HTML file:** Save the provided HTML code as an `.html` file (e.g., `qkd_demo.html`) and open it in a web browser.

2. **Adjust Settings:**

   * **Key Length (bits):** Choose how many "photons" Alice will send (between 4 and 64).

   * **Eve's Eavesdrop Chance (%):** Set the probability (0-100) that Eve will attempt to intercept each photon.

3. **Follow the Steps:**

   * Click "**1. Alice Generates Bits & Bases**" to start the process.

   * Click "**2. Eve Intercepts (if enabled)**" to simulate Eve's actions.

   * Click "**3. Bob Measures Photons**" to simulate Bob's measurements.

   * Click "**4. Reconcile Keys & Check for Eavesdropping**" to see if a shared key was established and if eavesdropping was detected.

4. **Observe:** Pay attention to the output boxes and the final message to understand how Eve's interference (when her chance is > 0 and she chooses a different basis) leads to detectable errors.

## Technical Details

* **HTML5:** Provides the structure of the web page.

* **Tailwind CSS:** Used for responsive and modern styling.

* **JavaScript:** Implements the core logic of the QKD simulation and handles user interactions.

* **Simulated Randomness:** `Math.random()` is used to simulate the quantum randomness in bit generation, basis choices, and Eve's interception.
