# Add Pallets to Your Runtime

### 1) I import the Nicks Pallet on the Node Template -> runtime/Cargo.toml
```
[dependencies.pallet-nicks]
default-features = false
version = '4.0.0-dev'
git = 'https://github.com/paritytech/substrate.git'
tag = 'monthly-2021-09+1'

[features]
default = ["std"]
std = [
    #--snip--
    'pallet-nicks/std',
    #--snip--
]

```
### 2) I added the following code to the Node Template -> runtime/src/lib.rs
```
/// Add this code block to your template for Nicks:
parameter_types! {
    // Choose a fee that incentivizes desireable behavior.
    pub const NickReservationFee: u128 = 100;
    pub const MinNickLength: u32 = 8;
    // Maximum bounds on storage are important to secure your chain.
    pub const MaxNickLength: u32 = 32;
}

impl pallet_nicks::Config for Runtime {
    // The Balances pallet implements the ReservableCurrency trait.
    // `Balances` is defined in `construct_runtimes!` macro. See below.
    // https://substrate.dev/rustdocs/latest/pallet_balances/index.html#implementations-2
    type Currency = Balances;

    // Use the NickReservationFee from the parameter_types block.
    type ReservationFee = NickReservationFee;

    // No action is taken when deposits are forfeited.
    type Slashed = ();

    // Configure the FRAME System Root origin as the Nick pallet admin.
    // https://substrate.dev/rustdocs/latest/frame_system/enum.RawOrigin.html#variant.Root
    type ForceOrigin = frame_system::EnsureRoot<AccountId>;

    // Use the MinNickLength from the parameter_types block.
    type MinLength = MinNickLength;

    // Use the MaxNickLength from the parameter_types block.
    type MaxLength = MaxNickLength;

    // The ubiquitous event type.
    type Event = Event;
}

construct_runtime!(
    /* --snip-- */

    Nicks: pallet_nicks::{Pallet, Call, Storage, Event<T>},
);
```

### 3) Using the Nicks Pallet
![](https://raw.githubusercontent.com/ysongh/Polkadot-Hack-Challenges-2021/master/PolkadotGeneralChallenges/AddPalletsToYourRuntime/screenshot1.png)

### 4) Result
![](https://raw.githubusercontent.com/ysongh/Polkadot-Hack-Challenges-2021/master/PolkadotGeneralChallenges/AddPalletsToYourRuntime/screenshot2.png)