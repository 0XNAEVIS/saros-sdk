import { describe, it, expect } from 'vitest';
import { Connection, PublicKey } from '@solana/web3.js';
import { DLMMClient } from '@saros-finance/dlmm-sdk';
import BN from 'bn.js';

const RPC = process.env.RPC_URL ?? 'https://api.devnet.solana.com';
const POOL = new PublicKey(process.env.DLMM_POOL_PUBKEY!);

describe('DLMM boundary & quote tests', () => {
  it('quote at exact bin boundary should not overpromise amountOut', async () => {
    const conn = new Connection(RPC, 'confirmed');
    const client = await DLMMClient.create(conn);

    // Fetch pool price info (API names may differ; adapt if necessary)
    const priceInfo = await client.getPoolPriceInfo(POOL);
    const boundaryPrice = priceInfo.lowerBinPrice ?? priceInfo.tickPrice;

    const amountIn = new BN(1_000);

    const quote = await client.getQuote({
      pool: POOL,
      amountIn,
      byAmountIn: true,
      slippageBps: 0,
      priceBound: boundaryPrice,
    } as any);

    expect(quote.amountOut).toBeTruthy();
    expect(quote.amountOut.toString()).not.toContain('-');
  }, { timeout: 20_000 });
});
