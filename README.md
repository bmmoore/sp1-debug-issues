# sp1-debug-issues

Forked for debugging mm checker.

Running as `cargo run --release -- cpu` with `bad-stdin.bin` copied to `stdin.bin` fails with output
```
thread '<unnamed>' panicked at /home/brandon/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-core-machine-4.0.1/src/utils/mod.rs:86:17:
fixed log2 rows is too small: got 283364, expected 262144
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
thread '<unnamed>' panicked at /home/brandon/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-core-machine-4.0.1/src/utils/prove.rs:430:85:
called `Result::unwrap()` on an `Err` value: Any { .. }
thread 'main' panicked at /home/brandon/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-prover-4.0.1/src/lib.rs:372:64:
called `Result::unwrap()` on an `Err` value: Any { .. }
```

Running with `good-stdin.bin` copied to `stdin.bin` succeeds
```
Successfully generated proof!
Successfully verified proof!
```
`good-stdin2.bin` and `good-stdin3.bin` are additional good inputs at larger
sizes than `good-sdin.bin`, with a similar size and a larger size than `bad-stdin.bin`.

Also, `cargo run --release -- mock` mode fails for both good and bad input with a different error:
```
Successfully generated proof!
thread 'main' panicked at /home/brandon/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-stark-4.0.1/src/air/public_values.rs:105:26:
range end index 176 out of range for slice of length 0
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

## Steps

First, please check if your issue isn't one in the [common issues](https://docs.succinct.xyz/docs/developers/common-issues) page in our docs.

If not, please follow these steps:

* Fork this repo to your local machine
* Please prepending `SP1_DUMP=1` when running your normal program/script, it will output the `program.bin` and `stdin.bin` files to the current directory.
  Please note these 2 files are only generated when generating proofs (not in execution only mode).
* Then copy them to this repository
* Run the following commands in sequence:
 
## Go through these commands

SP1 can be used in several different modes: Mock, CPU, GPU and the Prover Network. To help debug the source of the issue and determine if it's specific to a prover mode, please follow the steps below (moving onto the next stage if the previous one succeeds). Let us know which stage your proof failed in.

* **Mock mode**  
  Run `cargo run --release -- mock`. If this fails, your error is within the execution of the program (and is hardware-agnostic).
* **Prover Network**  
  If you are generating a proof in CPU/GPU mode and are seeing failures, you should attempt generating a proof on the Prover Network, by running `cargo run --release -- network`. If [running your progam](https://docs.succinct.xyz/docs/generating-proofs/prover-network/usage) on the Prover Network works, the issue is likely due to an issue on your local setup. If it fails, this indicates something is wrong with the Prover Network when generating proofs.  
  
  > [!NOTE]
  > To test on the Prover Network, you need to have an API key and set it to the `NETWORK_PRIVATE_KEY` environment variable
* **GPU mode**  
  If you have a [supported GPU](https://docs.succinct.xyz/docs/generating-proofs/hardware-acceleration/cuda), Run `cargo run --release -- gpu` and see if it works.
* **CPU mode**  
  If testing on a GPU fails, Try on the CPU by running  `cargo run --release -- cpu` and see if it works.


If any of the above fail, report the command you ran and their output in the section below.

If all of the above succeed, the issue is likely due to the setup of your program not supplying valid input to the program (as you've now ran a stdin & program ELF that succeeds).

## Describe command for reproducible issue
 
**Please list the command here where you run into an issue**
 
* List the command: ...
* Write the terminal output: ...
* Write machine specs: ...
