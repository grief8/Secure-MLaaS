# cluster-inference
For safety concern, we have to build a reliable communication architecture to ensure that the service is correctly initialized in the cloud and not modified and the secure point-to-point channel is built between user and Secure MLaaS. 


## Port distribution
| Name        | ip    |  port(communication\|attestation)  |
| --------   | -----:   | :----: |
| Slave        |    IP   |   22100---, 32100---    |
| scheduler        |   IP    |   22000, 32000   |
| Overlay        |   IP    |   21000---, 31000---    |


## Command
Run `run.sh` to start the service, or run the following commands step by step.
<!-- 
```

# Setup environment
cd cluster-inference
source environment.sh

PartID=$1
TARGET_NAME=sgx-task-enclave
TARGET_DIR=`pwd`/sgx-task-enclave/target/x86_64-fortanix-unknown-sgx/debug
TARGET=$TARGET_DIR/$TARGET_NAME
TARGET_SGX=$TARGET_DIR/$TARGET_NAME.sgxs
TARGET_SIG=$TARGET_DIR/$TARGET_NAME.sig
KEY=ra-enclave/examples/data/vendor-keys/private_key.pem

# Build and sign enclave
(cd sgx-task-enclave && cargo build --target x86_64-fortanix-unknown-sgx ) && \
ftxsgx-elf2sgxs $TARGET --heap-size 0x10000000 --stack-size 0x800000 --threads 8 \
    --debug --output $TARGET_SGX && \
#sgxs-sign --key $KEY $TARGET_SGX $TARGET_DIR/$TARGET_NAME.sig -d --xfrm 7/0 --isvprodid 0 --isvsvn 0
sgxs-sign --key $KEY $TARGET_SGX $TARGET_SIG -d --xfrm 7/0 --isvprodid 0 --isvsvn 0

# Build client
# (cd attest-client && cargo build --target x86_64-unknown-linux-gnu  --example attest_client --features verbose )

# Build SP
# (cd ra-sp && cargo build --target x86_64-unknown-linux-gnu )

# Run enclave with the default runner
ftxsgx-runner --signature coresident $TARGET &

# Run client
#(cd ra-client && cargo run --target x86_64-unknown-linux-gnu -Zfeatures=itarget --features "verbose" --example tls-client -- -e 127.0.0.1:7710 -s 127.0.0.1:1234 -n 0) &
(cd ra-client && cargo run --target x86_64-unknown-linux-gnu  --features "verbose" --example tls-client -- -e 127.0.0.1:7711 -s 127.0.0.1:1234  -n 1) &

# Run SP
(cd ra-sp && cargo run --target x86_64-unknown-linux-gnu  --example tls-sp   --features "verbose")

# Build Scheduler
cd scheduler && cargo run \
--target x86_64-fortanix-unknown-sgx \
--features verbose

file=full_transition.txt
for i in {0..50};
do
    echo $i
    echo `ftxsgx-runner part1/target/x86_64-fortanix-unknown-sgx/debug/sgx-demo.sgxs` >> ${file} &
    sleep 0.5
    echo `ftxsgx-runner part0/target/x86_64-fortanix-unknown-sgx/debug/sgx-demo.sgxs` >> ${file}
done;
``` -->