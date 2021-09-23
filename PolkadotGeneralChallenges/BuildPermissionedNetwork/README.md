# Build a Permissioned Network

### 1) I add the node-authorization pallet.
In Substrate Node Template `runtime/Cargo.toml`
```
[dependencies.pallet-node-authorization]
default-features = false
version = '3.0.0'

[features]
default = ['std']
std = [
    #--snip--
    'pallet-node-authorization/std',
    #--snip--
]
```

### 2) I add the following code to simulate the governance in my simple blockchain
`runtime/src/lib.rs`
```
/* --snip-- */

use frame_system::EnsureRoot;

/* --snip-- */

parameter_types! {
    pub const MaxWellKnownNodes: u32 = 8;
    pub const MaxPeerIdLength: u32 = 128;
}

impl pallet_node_authorization::Config for Runtime {
    type Event = Event;
    type MaxWellKnownNodes = MaxWellKnownNodes;
    type MaxPeerIdLength = MaxPeerIdLength;
    type AddOrigin = EnsureRoot<AccountId>;
    type RemoveOrigin = EnsureRoot<AccountId>;
    type SwapOrigin = EnsureRoot<AccountId>;
    type ResetOrigin = EnsureRoot<AccountId>;
    type WeightInfo = ();
}

construct_runtime!(
    pub enum Runtime where
        Block = Block,
        NodeBlock = opaque::Block,
        UncheckedExtrinsic = UncheckedExtrinsic
    {
        /* --snip-- */

        NodeAuthorization: pallet_node_authorization::{Module, Call, Storage, Event<T>, Config<T>},

        /* --snip-- */

    }
);
```

### 3) I add genesis storage for the pallet
`node/cargo.toml`
```
[dependencies]
#--snip--
bs58 = "0.4.0"
#--snip--
```

`node/src/chain_spec.rs`
```
/* --snip-- */
use sp_core::OpaquePeerId; // A struct wraps Vec<u8>, represents as our `PeerId`.
use node_template_runtime::NodeAuthorizationConfig; // The genesis config that serves for our pallet.
/* --snip-- */

/// Configure initial storage state for FRAME modules.
fn testnet_genesis(
    wasm_binary: &[u8],
    initial_authorities: Vec<(AuraId, GrandpaId)>,
    root_key: AccountId,
    endowed_accounts: Vec<AccountId>,
    _enable_println: bool,
) -> GenesisConfig {

    /* --snip-- */

    pallet_node_authorization: Some(NodeAuthorizationConfig {
        nodes: vec![
            (
                OpaquePeerId(bs58::decode("12D3KooWBmAwcd4PJNJvfV89HwE48nwkRmAgo8Vy3uQEyNNHBox2").into_vec().unwrap()),
                endowed_accounts[0].clone()
            ),
            (
                OpaquePeerId(bs58::decode("12D3KooWQYV9dGMFoRzNStwpXztXaBUjtPqi6aU76ZgUriHhKust").into_vec().unwrap()),
                endowed_accounts[1].clone()
            ),
        ],
    }),

    /* --snip-- */

}
```