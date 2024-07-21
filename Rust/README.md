# Python vs Rust Lambda Performance Comparison

This experiment was inspired by two presentations and borrows heavily from both:

- **Lambda vs EC2 Comparison** by [Illya Kavaliou](https://github.com/ikavaliou-mg/lambda-ec2-ecs-comparison/tree/main)
- **Rustifying Serverless** by [Efi Merdler-Kravitz](https://www.youtube.com/watch?v=Mdh_2PXe9i8)

## Overview

In this experiment, I compare the performance of two simple AWS Lambda functions. Both functions perform the same task: 
generating a UUID, saving it as an object in an S3 bucket, and then storing the UUID in a DynamoDB table. The only 
difference is that one Lambda function is written in Rust and the other in Python.

Based on Efi's presentation, I anticipated that Rust would outperform Python, and I aimed to verify this through my own 
tests.

## Motivation

I find this topic interesting because I don't typically associate Rust with serverless computing and AWS Lambda, where 
interpreted languages like Python, Node.js, and TypeScript are more common. However, AWS's promotion of Rust for 
serverless applications prompted me to reassess my own biases. I wanted to see if I could replicate Efi's results using 
the method I'd seen Illya use in his presentation.

## Project Structure

This repository contains the files for the Rust part of my experiment. Contrary to my expectations, this turned out to 
be the easier part. Surprisingly, the Rust part was easier than anticipated and did not fully represent the complexity 
I expected in Rust development.

## Running the Experiment

To run the Rust Lambda function, follow these steps:

## Prerequisites

- Install [Rust](https://www.rust-lang.org/) and [Cargo](https://doc.rust-lang.org/cargo/).
- Install [AWS CLI](https://aws.amazon.com/cli/).


## set up IAM role & permissions

Ensure you have the necessary IAM role and permissions set up to allow your Lambda function to interact with S3 and DynamoDB.

## install musl

For compiling Rust Lambda functions, you'll need to install `musl`.

```bash

```

## install Rust & Cargo
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## install cargo-lambda, a tool that simplifies building and deploying AWS Lambda functions written in Rust

```bash
cargo install cargo-lambda
```

## the cargo.toml file: 
This lists the dependencies for the project. It's similar to the pyproject.toml used with Poetry in Python.

## the main.rs file: this is our lambda function, it performs the following functions
This is our Lambda function, performing the following tasks:

- Generate a UUID: Creates a unique identifier (UUID).
- Store Data in S3: Converts the UUID to bytes and stores it in an S3 bucket as a file with the UUID as the filename.
- Store Data in DynamoDB: Stores the UUID in a DynamoDB table as an item with the key "id" set to the UUID value.
- Return a JSON Response: Returns a JSON response containing the UUID.

This serves as an example of integrating AWS services (S3 and DynamoDB) using Rust, demonstrating basic operations like storing and retrieving data.

## compile the lambda function
Ensure you specify `--release` to reduce the size of the binary. Avoid using `--debug` as it will make the binary too large.

**Optional** If you've compiled previously, i try to always clean first.
```bash
cargo clean
```

## compile to binary

Rust is a compiled language so we need to compile the code to a binary that can be executed on AWS Lambda.
This is one of its key features
Rust compile is quite different from say Java, where you require a JVM to run the code. 
Rust compiles to a binary that can be run on any machine.


```bash
cargo lambda build --release --target x86_64-unknown-linux-musl
```

## Strip: 

we can further reduce the size of the binary by stripping it

```bash
strip target/lambda/aws_rust/bootstrap
```