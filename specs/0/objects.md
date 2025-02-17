
Container
```ts
class Container {
    public encodeToHex(forHash = false)
}
```

HashableContainer
```ts
abstract class HashableContainer extends Container {
    public calculateHash() {
        sha256(this.encodeToHex(forHash = true));
    }
}
```


Block
```ts
export class Block extends HashableContainer {
    public index: Uint64;
    public slotIndex: Uint64;
    public hash: Uint256;
    public previousHash: Uint256;
    public timestamp: Uint64;
    public minter: AddressHex;
    public signature: Signature;
    public body: BlockBody;
    public readonly version: PX = PX.A_00;
}

export class BlockBody extends Container {
    public transactions: Transaction[];
    public slashings: Slashing[];
}
```

Transaction:
```ts
export class Transaction extends HashableContainer {
    public txid: Uint256;
    public senderAddress: AddressHex;
    public recipientAddress: AddressHex;
    public amount: Uint64;
    public nonce: Uint64;
    public timestamp: Uint64;
    public input: Uint;
    public signature: Signature;
    public readonly version = PX.V_00;
}
```

HexAddress:
```ts

/*
<----------- Address (21 Bytes) ----------->

Address Version    Address Body
 | <-------------------------------------->
00 AF3B4C2D40D836269DD51D817351467470943FBF

*/
export class AddressHex extends FixedUint {
    public static readonly byteLength = 21;
}
```

Address32:
```ts
export class Address32 {

    private static address32Chars = '123456789abcdefghjklmnpqrstuvwyz';

    public static toAddressHex(address32: string) {

        const address32Data = "2" + address32.slice(4).toLowerCase();
        let decimalValue = 0n;
        for (let i = 0; i < address32Data.length; i++) {
            decimalValue = (decimalValue * 32n) + BigInt(this.address32Chars.indexOf(address32Data[i]));
        }

        return this.getType(address32).replace("x", "0") + decimalValue.toString(16).slice(1);
    }
    
    public static fromAddressHex(addressHex: AddressHex) {

        let decimalValue = BigInt('0x1' + addressHex.slice(1).toHex());
        let address32 = '';
        while (decimalValue > 0n) {
            address32 = this.address32Chars[Number(decimalValue % 32n)] + address32;
            decimalValue = decimalValue / 32n;
        }

        return "lc" + DataUtils.replaceAtIndex(addressHex.getType().toHex(), "0", "x", 1) + address32.slice(1);
    }

}
```

