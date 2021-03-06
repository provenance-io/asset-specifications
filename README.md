# asset-specifications
[Scope Specifications](https://docs.provenance.io/modules/metadata-module#scope-specification) for classified asset/NFT types.

This library exposes helper utilities for creating a collection of Provenance Blockchain msg write protos for creating:
- [Scope Specifications](https://docs.provenance.io/modules/metadata-module#scope-specification)
- [Record Specifications](https://docs.provenance.io/modules/metadata-module#record-specification)
- [Contract Specifications](https://docs.provenance.io/modules/metadata-module#contract-specification)

These core components comprise the baseline needed for generating new [Provenance Metadata Module Scopes](https://docs.provenance.io/modules/metadata-module#scope-data-structures).

## Status
[![Latest Release][release-badge]][release-latest]
[![Maven Central][maven-badge]][maven-url]
[![Apache 2.0 License][license-badge]][license-url]

[license-badge]: https://img.shields.io/github/license/provenance-io/asset-specifications.svg
[license-url]: https://github.com/provenance-io/asset-specifications/blob/main/LICENSE
[maven-badge]: https://maven-badges.herokuapp.com/maven-central/io.provenance.spec/asset-specs/badge.svg
[maven-url]: https://maven-badges.herokuapp.com/maven-central/io.provenance.spec/asset-specs
[release-badge]: https://img.shields.io/github/tag/provenance-io/asset-specifications.svg
[release-latest]: https://github.com/provenance-io/asset-specifications/releases/latest

### Specifications on Testnet

https://explorer.test.provenance.io/tx/E1B42D0ACCA2E6674E652C88CA3DC57789A5881558B4D46C91F149244D068BF8

NFT Examples: 

Collectable NFT: https://explorer.test.provenance.io/nft/78f034ed-9b7e-4a6e-af14-986bf41d2478
HELOC NFT: https://explorer.test.provenance.io/nft/985bd660-6d5a-4400-8de2-502830208121
Mortgage NFT: https://explorer.test.provenance.io/nft/ed14f4af-217c-48f9-8082-25ccf3547b33
Payable NFT: https://explorer.test.provenance.io/nft/bce44beb-e4e6-4115-8660-f20687b23da4
Personal Loan NFT: https://explorer.test.provenance.io/nft/8972656d-cd13-4a5d-86d0-942e2a9f0b
Property NFT: https://explorer.test.provenance.io/nft/9704e06c-43fe-4db9-bc27-bcb2d7fe54bb
Shareclass NFT: https://explorer.test.provenance.io/nft/c328573f-08cd-4cd8-956d-22d6ab45875c

### Release Process
- All new features merge to develop
- Code ready for release merges to main
- Only draft release artifacts from the main branch

### Using the asset-specs library

To create Provenance Blockchain msg protos that will onboard a scope specification, simply use the provided
[AssetSpecifications](specs/src/main/kotlin/io/provenance/spec/AssetSpecification.kt/) object, or refer to a specification
directly:

```kotlin
import com.google.protobuf.Message
import io.provenance.scope.util.toUuid
import io.provenance.spec.AssetSpecification
import io.provenance.spec.AssetSpecifications
import io.provenance.spec.ContractSpecConfig
import io.provenance.spec.HELOCSpecification
import io.provenance.spec.RecordSpecConfig
import io.provenance.spec.ScopeSpecConfig

// To generate all messages, owned by the same address
fun generateAllSpecificationMessages(specOwnerBech32Address: String): List<Message> = AssetSpecifications
    .flatMap { it.specificationMsgs(ownerAddress = specOwnerBech32Address) }

// To generate a single type's messages, owned by an address
fun generateHelocSpecificationMessages(specOwnerBech32Address: String): List<Message> = HELOCSpecification
    .specificationMsgs(ownerAddress = specOwnerBech32Address)

// Creating your own specification is easy, too!
object MyNewSpecification : AssetSpecification {
    override val scopeSpecConfig: ScopeSpecConfig = ScopeSpecConfig(
        // UUID generation is mandatory for creating a new scope spec.  This will be used to generate its bech32 address
        id = "798e4bac-d2df-11ec-9b56-ef6cd818428b".toUuid(),
        name = "My New NFT",
        description = "Provenance Blockchain's new NFT",
        websiteUrl = "<enter your own website here>",
    )

    // Multiple contract specs are not a requirement, but are allowed and supported. Simply add more than one item to the
    // list and it will appear in the generated messages when invoking specificationMsgs() on this object
    override val contractSpecConfigs: List<ContractSpecConfig> = listOf(
        ContractSpecConfig(
            // UUID generation is mandatory for creating a new contract spec.  This will be used to generate its bech32 address
            id = "cd849fea-d2df-11ec-a8be-ef96042c1133".toUuid(),
            contractClassname = "com.mycompany.spec.OnboardMyNewContractSpec",
            name = "Onboard My New NFT",
            description = "Mint My New NFT provided by My Cool Company",
            websiteUrl = "<enter your own website here>",
        )
    )

    // Multiple record specs are not a requirement, but are allowed and supported. Simply add more than one item to the
    // list and it will appear in the generated messages when invoking specificationMsgs() on this object
    override val recordSpecConfigs: List<RecordSpecConfig> = listOf(
        RecordSpecConfig(
            // UUID generation is mandatory for creating a new record spec.  This will be used to generate its bech32 address
            id = "2abdbdb8-d2e0-11ec-a14a-1f9a19c1cfe4".toUuid(),
            name = "mynewnft",
            // Provenance Blockchain provides an asset model at this repository, but any protobuf data structure can be used:
            // https://github.com/provenance-io/metadata-asset-model
            typeClassname = "io.provenance.model.v1.Asset",
            // With multiple record specs in a more complex structure, direct contract specification uuid values may
            // need to be provided here instead of just referencing the first entry.
            contractSpecId = contractSpecConfigs.first().id,
        )
    )
}
```
