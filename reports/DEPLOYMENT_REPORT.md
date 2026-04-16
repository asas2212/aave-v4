# Aave V4 Deployment Report — Anvil Local Testnet

**Date:** 2026-04-16  
**Chain:** Anvil (Chain ID 31337)  
**Deployer:** `0x70997970C51812dc3A010C7d01b50e0d17dc79C8`  
**Config Engine Version:** AaveV4ConfigEngine (new)  
**Foundry Version:** 1.5.1-stable (b0a9dd9ced)

---

## 1. Executive Summary

Full Aave V4 deployment executed successfully on a local Anvil testnet using the new config engine. All contracts deployed correctly via CREATE2, all roles were assigned as expected, and both the deployment test suite (36/36 tests) and config engine test suite (192/192 tests) passed with zero failures.

---

## 2. Deployment Steps Executed

### 2.1 Dry-Run: LibraryPreCompile

**Command:**

```bash
forge script scripts/LibraryPreCompile.s.sol \
  --rpc-url http://127.0.0.1:8545 --ffi \
  --sender 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 --unlocked
```

**Result:** Simulation completed successfully.  
**Estimated Gas:** 4,025,340  
**LiquidationLogic Address:** `0x818E84198224535FAeaEc1b583d3Ff6b812A5AF3`

### 2.2 Broadcast: LibraryPreCompile

**Result:** `ONCHAIN EXECUTION COMPLETE & SUCCESSFUL`  
`FOUNDRY_LIBRARIES` env variable written to `.env` for library linking.

### 2.3 Dry-Run: AaveV4DeployAnvil (Full Deployment)

**Command:**

```bash
forge script scripts/deploy/examples/AaveV4DeployAnvil.s.sol \
  --rpc-url http://127.0.0.1:8545 \
  --sender 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 --unlocked
```

**Result:** Simulation completed successfully.  
**Estimated Gas:** 95,520,573  
**Estimated Cost:** 0.191 ETH (at 2 gwei gas price)

### 2.4 Broadcast: AaveV4DeployAnvil (Full Deployment)

**Result:** `ONCHAIN EXECUTION COMPLETE & SUCCESSFUL`

---

## 3. Deployed Contracts

| Contract                       | Address                                      | Verified |
| ------------------------------ | -------------------------------------------- | -------- |
| **LiquidationLogic** (library) | `0x818E84198224535FAeaEc1b583d3Ff6b812A5AF3` | Yes      |
| **AccessManager**              | `0x21fa99Ec57F137f7D198a1EAC0a6bFB819eEbad5` | Yes      |
| **HubConfigurator**            | `0xBb293468284250ed34d1397c2369e60f56dA67Cc` | Yes      |
| **SpokeConfigurator**          | `0xbe1Ac240Bf9d84364B388BB008b44921f1C01Fb8` | Yes      |
| **TreasurySpoke**              | `0xF36B3aca6F15dED39E02b61e8A0B7E15041f54bb` | Yes      |

### Hubs

| Label    | Hub (Proxy)                                  | Implementation                               | InterestRateStrategy                         |
| -------- | -------------------------------------------- | -------------------------------------------- | -------------------------------------------- |
| **core** | `0x4F50f3C9c11934734b58C0d21C8861aF8c67a6F8` | `0x7DF422f1B16479D8F40574bEB8acb7ac7F0b07a5` | `0x3fcfFf999BE0240cAB1f62Ac611f6eaE05f1Df53` |
| **test** | `0xe267a24D250B0C6bc826cbbDc3e3172b9fD3b86A` | `0xA6712A45674d37c4e4499E882345d7AEeC2c2D90` | `0xFaD9b294584D8cf5b1a232C662B313d2d430835C` |

### Spokes

| Label       | Spoke (Proxy)                                | Implementation                               | AaveOracle                                   |
| ----------- | -------------------------------------------- | -------------------------------------------- | -------------------------------------------- |
| **mainnet** | `0xeD1bF1B52E6BEed649d2da09a5530CFaf1E48eF7` | `0xa0d60331E2420516749D9364F729CDb3Bed22568` | `0x5CAF8Fa1f2a350A273cB7d6691f3c5Fcbc79C6B4` |
| **test**    | `0xf55093F5B4256FfE829bf3d6AF2fAd8057a57b0C` | `0xe598A10655a93A4e6Ef61EA57933Ec0C8488cb6e` | `0xFC580eAFB4393196cD9339e7008850120da36eA9` |
| **prime**   | `0x1698b90635473fB14660249e09584232382C907E` | `0xAb5AbDB71C544f17Ca85915E6cCdf18EAe960750` | `0x585f5B6Ad17AB96ED3dA78C5Feb11B2F5674bDe6` |

### Gateways & Position Managers

| Contract                  | Address                                      | Verified |
| ------------------------- | -------------------------------------------- | -------- |
| **NativeTokenGateway**    | `0x86A61e7C95D602F3e5b316d9920f56974ad87F6e` | Yes      |
| **SignatureGateway**      | `0xA88e36e6C23d0706B05DE00D2a35e28dfced3736` | Yes      |
| **GiverPositionManager**  | `0x57A11D5D0aeC7E009b957708944Fec4f19277Cb5` | Yes      |
| **TakerPositionManager**  | `0x28e3312e65D5653a5Fa0f9146ACf9385f2C03192` | Yes      |
| **ConfigPositionManager** | `0xD56D553120B4A30169E16111e6774cD4a55E1399` | Yes      |

---

## 4. Role Assignments Verification

All roles verified on-chain via `AccessManager.hasRole()`:

| Role ID | Role Name                              | Granted To                            | Verified |
| ------- | -------------------------------------- | ------------------------------------- | -------- |
| 0       | `DEFAULT_ADMIN_ROLE`                   | Deployer                              | Yes      |
| 101     | `HUB_CONFIGURATOR_ROLE`                | Deployer + HubConfigurator contract   | Yes      |
| 102     | `HUB_FEE_MINTER_ROLE`                  | Deployer (hubAdmin)                   | Yes      |
| 103     | `HUB_DEFICIT_ELIMINATOR_ROLE`          | Deployer (hubAdmin)                   | Yes      |
| 200     | `HUB_CONFIGURATOR_DOMAIN_ADMIN_ROLE`   | Deployer (hubConfiguratorAdmin)       | Yes      |
| 301     | `SPOKE_CONFIGURATOR_ROLE`              | Deployer + SpokeConfigurator contract | Yes      |
| 400     | `SPOKE_CONFIGURATOR_DOMAIN_ADMIN_ROLE` | `address(1)` (spokeConfiguratorAdmin) | Yes      |

### Role Labels (on-chain via `getLabelOfRole()`)

All labels verified:

- Role 100 → `HUB_DOMAIN_ADMIN_ROLE`
- Role 101 → `HUB_CONFIGURATOR_ROLE`
- Role 102 → `HUB_FEE_MINTER_ROLE`
- Role 103 → `HUB_DEFICIT_ELIMINATOR_ROLE`
- Role 200 → `HUB_CONFIGURATOR_DOMAIN_ADMIN_ROLE`
- Role 300 → `SPOKE_DOMAIN_ADMIN_ROLE`
- Role 301 → `SPOKE_CONFIGURATOR_ROLE`
- Role 400 → `SPOKE_CONFIGURATOR_DOMAIN_ADMIN_ROLE`

**Note:** `DEFAULT_ADMIN_ROLE` (role 0) does not have a label via `getLabelOfRole()` — it reverts with a custom error. This is expected behavior since role 0 is a built-in OpenZeppelin role and is not labeled by the Aave deployment procedures.

---

## 5. Test Results

### Deployment Test Suite (`tests/deployments/AaveV4BatchDeployment.t.sol`)

- **Result:** 36 passed, 0 failed, 0 skipped
- **Duration:** 311.50s (708.18s CPU time)
- Tests cover: full deployment, deployment without roles, without gateways, without hubs/spokes, zero-address edge cases, duplicate labels, and more.

### Config Engine Test Suite (`tests/config-engine/*`)

- **Result:** 192 passed, 0 failed, 0 skipped
- **Duration:** 8.78s (22.76s CPU time)
- **Test suites:** 7 suites covering Hub actions, Spoke actions, AccessManager actions, PositionManager actions, asset listings, config updates, role memberships, and more.

---

## 6. Issues Encountered

### Issue 1: Anvil Transaction Receipt Hang (Severity: Medium)

**Description:** When broadcasting the main deployment script to Anvil without `--block-time`, the forge script hung indefinitely waiting for transaction receipts. The log showed repeated `tx is still known to the node, waiting for receipt (3 tries remaining)` messages for many minutes.

**Root Cause:** Anvil's default auto-mining mode did not reliably mine blocks for the large batch of deployment transactions sent by forge script. The deployment script generates ~95M gas worth of transactions in a single batch, and Anvil's default behavior struggled to process them all.

**Resolution:** Restarted Anvil with `--block-time 1` (mine a new block every second) and `--gas-limit 300000000` (increased block gas limit). The deployment then completed successfully.

**Recommendation:** When deploying Aave V4 to a local Anvil instance, always use:

```bash
anvil --block-time 1 --gas-limit 300000000
```

### Issue 2: Stale FOUNDRY_LIBRARIES After Dry-Run (Severity: Low)

**Description:** Running `LibraryPreCompile.s.sol` in dry-run mode (without `--broadcast`) still wrote `FOUNDRY_LIBRARIES` to `.env` via FFI. When subsequently running with `--broadcast` on a fresh Anvil (where the library wasn't actually deployed), the script detected the stale entry and required a retry.

**Root Cause:** The `_deployAndWriteLibrariesConfig()` function in `SpokeDeployUtils.sol` uses FFI to append to `.env`, and this FFI call executes even during simulation mode. The library's built-in retry mechanism handled this correctly by removing the stale entry and asking for a re-run.

**Resolution:** Running the script a second time after the stale entry was cleaned resolved the issue. The script's built-in detection logic (`_librariesPathExists()` + code length check + `_deleteLibrariesPath()`) worked as designed.

**Recommendation:** When switching between dry-run and broadcast, or between different Anvil instances, be aware that `.env` may contain stale `FOUNDRY_LIBRARIES` entries. The script handles this gracefully but requires one extra invocation.

### Issue 3: Etherscan Config Warning for `bnb` Chain (Severity: Informational)

**Description:** Every forge script invocation logged: `failed to get etherscan config err=MissingUrlOrChain(" for Etherscan config with unknown alias 'bnb'")`

**Root Cause:** The `foundry.toml` defines `[etherscan]` entries including `bnb`, but Foundry's built-in chain resolution doesn't recognize `bnb` as an alias for BSC (chain ID 56).

**Resolution:** No action needed for local deployment. For production, this warning can be suppressed by using the alias `bsc` instead of `bnb` in `foundry.toml`, or by ignoring it since it does not affect deployment behavior.

### Issue 4: Compiler Warnings — Unreachable Code (Severity: Informational)

**Description:** The compiler produced warnings about unreachable code in `AaveV4DeployBatchBase.s.sol` at lines 41, 45, 57, and 60.

**Root Cause:** These warnings appear related to the `_validateChainId()` call at the top of `run()` which can revert, making subsequent code technically unreachable in the failure path. The Solidity compiler flags this conservatively.

**Resolution:** No functional impact. The code executes correctly when the chain ID matches.

### Issue 5: Dynamic Test Linking Preprocessor Warning (Severity: Informational)

**Description:** During compilation, a warning appeared: `failed preprocessing err=solar run failed: error: identifier 'VmSafe' already declared` in `src/deployments/utils/Logger.sol`.

**Root Cause:** `Logger.sol` imports both `forge-std/StdJson.sol` and `forge-std/Vm.sol`, which both export the `VmSafe` interface. The Solar preprocessor (used for dynamic test linking) treats this as a conflict, though the standard Solidity compiler handles it fine.

**Resolution:** No functional impact on compilation or deployment. This is a tooling-level warning from the Solar preprocessor used in Foundry's dynamic test linking feature.

---

## 7. Deployment Configuration Used

```solidity
InputUtils.FullDeployInputs({
    accessManagerAdmin: address(0),       // defaults to deployer
    proxyAdminOwner: address(0),          // defaults to deployer
    hubAdmin: address(0),                 // defaults to deployer
    hubConfiguratorAdmin: address(0),     // defaults to deployer
    treasurySpokeOwner: address(0),       // defaults to deployer
    spokeAdmin: address(0),              // defaults to deployer
    spokeConfiguratorAdmin: address(1),   // explicit non-zero
    gatewayOwner: address(2),             // explicit non-zero
    positionManagerOwner: address(3),     // explicit non-zero
    nativeWrapper: weth,                  // deployed WETH9
    deployNativeTokenGateway: true,
    deploySignatureGateway: true,
    deployPositionManagers: true,
    grantRoles: true,
    hubLabels: ["core", "test"],
    spokeLabels: ["mainnet", "test", "prime"],
    spokeMaxReservesLimits: [],           // empty = use defaults
    salt: keccak256("anvil-test")
});
```

---

## 8. Conclusion

The Aave V4 deployment infrastructure with the new config engine is fully functional. The deployment scripts correctly:

1. Deploy all contract batches in the correct order (AccessManager → Configurators → TreasurySpoke → Hubs → Spokes → Gateways → PositionManagers)
2. Configure all roles and permissions via the AccessManagerEnumerable
3. Label all roles on-chain for queryability
4. Generate structured JSON deployment reports
5. Support deterministic CREATE2 deployment with label-based salt derivation
6. Handle the two-step LiquidationLogic pre-deployment workflow

The config engine test suite validates all action categories (Hub, Spoke, AccessManager, PositionManager) with comprehensive coverage of partial updates, sentinel values, and edge cases.
